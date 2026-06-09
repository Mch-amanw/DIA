# Ticket : CRUD organisations et filiales – Spécification Technique

## 1. Modèle de données

### 1.1 Table `organizations`

Utilisation du modèle défini dans la fonctionnalité parente.

Contraintes à implémenter :
- FK `tenant_id` → `tenants.id`
- FK `parent_id` → `organizations.id`
- Vérification applicative que `parent.tenant_id = organization.tenant_id`
- Index sur :
  - `tenant_id`
  - `parent_id`
  - (`tenant_id`, `parent_id`)

Champ clé :
- `is_archived` (boolean, default false)

---

### 1.2 Table pivot `organization_user`

Structure :
- `organization_id` (FK)
- `user_id` (FK)
- `tenant_id` (FK)

Contraintes :
- Vérification que l’utilisateur appartient au tenant (`tenant_user`).
- Index sur (`tenant_id`, `user_id`).
- Unicité (`organization_id`, `user_id`).

---

## 2. Couche backend (Laravel)

### 2.1 Modèles Eloquent
- `Organization`
- Relation `parent()` (belongsTo)
- Relation `children()` (hasMany)
- Relation `users()` (belongsToMany via `organization_user`)

Global Scope obligatoire sur `tenant_id`.

---

### 2.2 Validation anti-cycle

Avant toute modification de `parent_id` :
- Vérification que le nouveau parent n’est pas un descendant.
- Implémentation via requête récursive PostgreSQL (CTE `WITH RECURSIVE`).

Rejet transactionnel si violation.

---

## 3. API REST

### 3.1 Organisations

- `GET /organizations`
  - Paramètres : `view=tree|list`, filtres (type, country, is_archived)

- `POST /organizations`
- `PUT /organizations/{id}`
- `DELETE /organizations/{id}`
  - Implémente archivage logique (`is_archived = true`).

---

### 3.2 Rattachement utilisateurs

- `POST /organizations/{id}/users`
  - Body : `user_id`

- `DELETE /organizations/{id}/users/{userId}`

Toutes les routes :
- Authentification obligatoire.
- Résolution du tenant actif via middleware.
- Vérification RBAC (Administrateur organisation ou Super Admin).

---

## 4. Intégration module Évaluations

- Vérification à la création d’une évaluation que :
  - `organization_id` appartient au tenant courant.
  - `organization.is_archived = false`.

Aucune modification de logique métier supplémentaire.

---

## 5. Journalisation

Chaque action déclenche un événement :
- `OrganizationCreated`
- `OrganizationUpdated`
- `OrganizationArchived`
- `OrganizationParentChanged`
- `OrganizationUserAttached`
- `OrganizationUserDetached`

Ces événements alimentent le module Audit.

---

## 6. Sécurité & isolation

- Global Scope Laravel sur `tenant_id`.
- Vérification systématique que toute entité manipulée appartient au tenant actif.
- Interdiction d’accès cross-tenant validée par tests automatisés.

Compatible avec :
- Mode Shared (filtrage par `tenant_id`).
- Mode Dedicated DB (même schéma).
- Mode Dedicated Infra (même logique applicative).

---

## 7. Performance

- Requêtes hiérarchiques via CTE récursif PostgreSQL.
- Pagination pour la vue liste.
- Objectif : affichage arborescence < 2 secondes.

---

## 8. Tests techniques

- Tests unitaires :
  - Validation anti-cycle.
  - Refus de création deuxième racine.
  - Refus rattachement cross-tenant.

- Tests d’intégration :
  - Isolation multi-tenant.
  - Archivage empêchant nouvelles évaluations.
  - Filtrage correct selon rattachement utilisateur.