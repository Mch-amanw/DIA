# Spécification Technique : Gestion des organisations & filiales

## 1. Modélisation des données

### 1.1 Table `organizations`

Champs principaux :

- `id` (UUID, PK)
- `tenant_id` (UUID, FK indexée)
- `parent_id` (UUID, nullable, FK vers organizations.id)
- `name` (varchar)
- `type` (enum ou varchar contrôlé)
- `country` (varchar 2 ou 3 caractères ISO)
- `metadata` (JSONB)
- `is_archived` (boolean, default false)
- `created_at`
- `updated_at`

Contraintes :
- Index composite (`tenant_id`, `parent_id`).
- Contrainte FK garantissant cohérence parent/tenant.

---

### 1.2 Rattachement utilisateurs

Table pivot existante `tenant_user` (tenant-level).

Nouvelle table pivot :

`organization_user`
- `organization_id` (FK)
- `user_id` (FK)
- `tenant_id` (FK, pour cohérence et indexation)

Contraintes :
- Vérification que `organization.tenant_id = tenant_user.tenant_id`.
- Index sur (`tenant_id`, `user_id`).

---

## 2. Isolation & sécurité

- Global Scope Laravel appliqué sur `tenant_id`.
- Middleware de résolution du tenant actif obligatoire.
- Validation systématique que toute entité manipulée appartient au tenant courant.
- Tests automatiques empêchant toute requête cross-tenant.

---

## 3. Gestion de la hiérarchie

### 3.1 Prévention des cycles

- Validation applicative avant mise à jour de `parent_id`.
- Vérification qu’un parent proposé n’est pas un descendant.

### 3.2 Requêtes hiérarchiques

Deux approches possibles (selon volumétrie) :
- Requêtes récursives PostgreSQL (CTE WITH RECURSIVE).
- Chargement lazy avec reconstruction côté backend.

V1 : utilisation CTE récursive PostgreSQL.

---

## 4. API REST

Endpoints principaux :

- `GET /organizations` (liste filtrée / arborescente)
- `POST /organizations`
- `PUT /organizations/{id}`
- `DELETE /organizations/{id}` (archive logique)
- `POST /organizations/{id}/users`
- `DELETE /organizations/{id}/users/{userId}`

Toutes les routes protégées par :
- Authentification
- Vérification rôle (RBAC)
- Contexte tenant actif

---

## 5. Intégration avec le module Évaluations

- Ajout champ `organization_id` (FK) dans la table `evaluations`.
- Contrainte FK avec `tenant_id` cohérent.
- Index sur (`tenant_id`, `organization_id`).

Validation métier :
- Refus de création d’évaluation si `organization.is_archived = true`.

---

## 6. Performance

- Index obligatoire sur `tenant_id`.
- Index sur `parent_id`.
- Requêtes arborescentes optimisées via CTE.
- Pagination pour la vue liste.

Objectif : affichage arborescence < 2 secondes.

---

## 7. Audit & traçabilité

Chaque action déclenche une entrée dans l’audit log :
- Création
- Modification
- Changement de parent
- Archivage
- Rattachement/détachement utilisateur

Données loggées :
- user_id
- tenant_id
- organization_id
- action
- timestamp

---

## 8. Tests

- Tests unitaires validation anti-cycle.
- Tests multi-tenant isolation.
- Tests d’accès restreint par organisation.
- Tests de non-régression sur archivage.

---

## 9. Compatibilité multi-mode d’isolation

### Shared
- Filtrage par `tenant_id`.

### Dedicated DB
- Même schéma sans changement.

### Dedicated Infra
- Déploiement isolé mais modèle identique.

Aucune divergence logique entre modes.