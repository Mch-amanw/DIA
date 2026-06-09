## Spécification Technique – Rôles & Permissions

### 1. Architecture

Fonctionnalité intégrée au module Authentification & Gestion des accès.
Implémentation via un modèle RBAC (Role-Based Access Control).

Composants concernés :
- RoleController
- PermissionMiddleware
- UserController
- Policy classes Laravel
- AuditListener

---

### 2. Modèle de données

Tables utilisées :

- roles
  - id
  - name
  - scope (platform | tenant)

- permissions
  - id
  - module
  - action

- role_permission
  - role_id
  - permission_id

- user_role
  - user_id
  - role_id
  - tenant_id

Contraintes :
- Index sur (tenant_id, user_id).
- Index sur (role_id, permission_id).
- Intégrité référentielle via clés étrangères.

En mode shared database : filtrage systématique par tenant_id.

---

### 3. Contrôle d’accès

#### 3.1 Middleware
Un middleware global :
- Vérifie l’authentification.
- Résout le tenant actif.
- Charge les rôles associés à l’utilisateur pour ce tenant.
- Vérifie la permission requise.

Retour :
- 401 si non authentifié.
- 403 si permission insuffisante.

#### 3.2 Policies Laravel
Utilisation de Policies par ressource (ex : EvaluationPolicy, ReferentialPolicy) pour encapsuler la logique métier complémentaire.

---

### 4. Gestion des rôles

#### 4.1 Attribution
- Endpoint sécurisé pour assigner ou retirer un rôle.
- Vérification que l’émetteur dispose d’une permission d’administration suffisante.
- Validation du scope (impossible d’attribuer un rôle plateforme sans permission globale).

#### 4.2 Journalisation
Toute action :
- assignRole
- removeRole
- updateRolePermissions

Déclenche un événement intercepté par AuditListener.

Données loggées :
- user_id cible
- acteur
- tenant_id
- ancien rôle
- nouveau rôle
- horodatage

---

### 5. Multi-tenancy

Compatibilité :
- Shared DB : filtrage automatique via tenant_id.
- DB dédiée : connexion dynamique.
- Infra dédiée : comportement identique.

La résolution du tenant actif est obligatoire avant toute vérification RBAC.

---

### 6. Performance

Objectifs :
- Vérification permission < 50 ms.

Optimisations :
- Cache en mémoire (par requête) des permissions utilisateur.
- Possibilité de cache Redis futur si nécessaire.

---

### 7. Sécurité

- Aucun endpoint métier sans middleware RBAC.
- Vérification serveur uniquement (jamais côté frontend seul).
- Tests unitaires couvrant :
  - Cas autorisés.
  - Cas refusés.
  - Contexte multi-tenant.

Couverture cible ≥ 80% sur logique RBAC.

---

### 8. Intégrations

- Module Organisations : résolution du tenant.
- Module Audit : enregistrement des modifications.
- Module Administration : configuration éventuelle des politiques.

---

### 9. Tests

- Tests unitaires :
  - Attribution rôle.
  - Vérification permission.
  - Cas multi-rôles.

- Tests d’intégration :
  - Accès API selon rôle.
  - Isolation inter-tenant.

- Tests de sécurité :
  - Tentatives d’escalade de privilèges.
  - Manipulation d’ID dans requêtes.