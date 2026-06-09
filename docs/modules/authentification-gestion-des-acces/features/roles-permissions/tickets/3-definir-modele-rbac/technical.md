## Ticket : Définir le modèle RBAC – Spécification technique

### 1. Architecture générale
Implémentation d’un modèle RBAC intégré au module Authentification & Gestion des accès.

Composants concernés :
- Modèles Eloquent : Role, Permission, User
- Tables pivot : role_permission, user_role
- Middleware PermissionMiddleware
- Policies Laravel
- Seeder initial des rôles et permissions

---

### 2. Modèle de données

#### 2.1 Table `roles`
- id (PK)
- name (string, unique)
- scope (enum: platform | tenant)
- created_at
- updated_at

Contraintes :
- Les rôles standards sont insérables via migration/seeder.
- Suppression interdite pour les rôles standards (contrainte logique applicative).

#### 2.2 Table `permissions`
- id (PK)
- module (string indexé)
- action (string indexé)
- created_at
- updated_at

Contrainte d’unicité :
- unique(module, action)

#### 2.3 Table `role_permission`
- role_id (FK → roles.id)
- permission_id (FK → permissions.id)

Index :
- (role_id, permission_id)

#### 2.4 Table `user_role`
- user_id (FK → users.id)
- role_id (FK → roles.id)
- tenant_id (nullable si scope = platform)

Index critiques :
- (user_id, tenant_id)
- (role_id)

En mode shared DB : filtrage systématique par tenant_id.

---

### 3. Initialisation (Seeding)

Un seeder doit :
1. Créer toutes les permissions selon la matrice (modules × actions).
2. Créer les rôles standards.
3. Associer les permissions par défaut à chaque rôle.

La matrice d’association doit être centralisée dans une configuration versionnée (ex : config/rbac.php).

---

### 4. Middleware RBAC

#### 4.1 Fonctionnement
Le PermissionMiddleware doit :
1. Vérifier l’authentification.
2. Résoudre le tenant actif.
3. Charger les rôles utilisateur pour ce tenant.
4. Agréger les permissions associées.
5. Vérifier la présence de la permission requise.

Retour :
- 401 si non authentifié.
- 403 si permission insuffisante.

#### 4.2 Optimisation
- Cache des permissions en mémoire pendant le cycle de requête.
- Structure interne : tableau indexé par "module.action".

Objectif performance : vérification < 50 ms.

---

### 5. Policies Laravel

Pour chaque ressource métier (ex : Evaluation, Referential), implémenter une Policy dédiée.

Rôle des Policies :
- Vérification complémentaire contextuelle (ex : accès uniquement aux ressources du tenant actif).
- Encapsulation de règles spécifiques non couvertes par le simple couple module/action.

---

### 6. Multi-tenancy

Compatibilité requise avec :
- Shared DB : filtrage par tenant_id obligatoire.
- DB dédiée : résolution dynamique connexion.
- Infra dédiée : configuration identique.

La résolution du tenant doit être effectuée avant toute vérification RBAC.

---

### 7. Journalisation

Les actions suivantes déclenchent un événement :
- assignRole
- removeRole
- updateRolePermissions

Événement capté par AuditListener.

Données journalisées :
- acteur (user_id)
- utilisateur cible
- tenant_id
- rôle ajouté/supprimé
- horodatage

Audit en write-only (non modifiable).

---

### 8. Sécurité

- Aucun endpoint métier sans middleware RBAC.
- Vérification exclusivement côté backend.
- Interdiction d’attribuer un rôle platform sans disposer d’un rôle platform adéquat.
- Vérification systématique du statut utilisateur (actif requis).

---

### 9. Tests techniques

À implémenter :
- Tests unitaires sur agrégation des permissions.
- Tests d’intégration sur accès API autorisé/refusé.
- Tests multi-tenant (isolation stricte).
- Tests d’escalade de privilèges.

Couverture cible ≥ 80% sur la logique RBAC.

---

### 10. Dépendances

- Module Organisations (résolution tenant).
- Middleware d’authentification.
- Module Audit.

Ce ticket constitue la fondation obligatoire pour sécuriser l’ensemble des modules métier.