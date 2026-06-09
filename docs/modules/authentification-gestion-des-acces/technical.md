# Module : Authentification & Gestion des accès – Spécification Technique

## 1. Architecture applicative

### 1.1 Positionnement
Module backend Laravel exposant des endpoints REST sécurisés.
Intégré au monolithe modulaire.

Composants principaux :
- AuthController
- SSOController
- MFAController
- UserController
- RoleController
- PermissionMiddleware
- AuditListener

---

## 2. Modèle de données (logique)

### Tables principales
- users
  - id
  - tenant_id
  - firstname
  - lastname
  - email
  - password_hash
  - status
  - preferred_language
  - mfa_enabled
  - created_at
  - updated_at

- roles
  - id
  - name
  - scope (platform / tenant)

- permissions
  - id
  - module
  - action

- role_permission
- user_role

- login_audit_logs
  - user_id
  - tenant_id
  - ip_address
  - user_agent
  - status
  - created_at

Isolation via tenant_id en mode shared.

---

## 3. Authentification

### 3.1 Email / Mot de passe
- Hashage sécurisé (bcrypt/argon2).
- Rate limiting via middleware Laravel.
- Tokens via Laravel Sanctum ou JWT sécurisé.
- Expiration configurable.

### 3.2 MFA
- OTP basé sur TOTP.
- Secrets stockés chiffrés.
- Codes de récupération optionnels.

### 3.3 SSO
- Intégration SAML 2.0.
- OAuth2 / OpenID Connect pour Azure AD, Google, Okta.
- Mapping attributs configurable par tenant.
- Vérification signature et certificats.

Secrets stockés dans AWS Secrets Manager.

---

## 4. Contrôle d’accès

### 4.1 Middleware
- Vérification authentification.
- Vérification tenant_id.
- Vérification rôle + permission.

Politique RBAC centralisée.

### 4.2 Multi-tenancy
Compatible avec :
- Shared DB : filtrage systématique par tenant_id.
- DB dédiée : connexion dynamique selon tenant.
- Infra dédiée : configuration identique, variable d’environnement.

---

## 5. Sécurité

- Chiffrement TLS obligatoire.
- Données sensibles chiffrées au repos.
- Protection CSRF.
- Rotation possible des clés.
- Blocage automatique après X tentatives échouées.

---

## 6. Audit & Logs

- Logs applicatifs vers CloudWatch.
- Indexation possible dans OpenSearch.
- Audit trail non modifiable (write-only pattern).
- Exports via génération asynchrone (SQS + Queue).

---

## 7. Performance & SLA

Objectifs :
- Login < 500 ms hors SSO externe.
- Vérification permission < 50 ms.

Scalabilité horizontale via ECS Fargate.

---

## 8. Intégrations

- Module Organisations (tenant resolution).
- Module Administration (politiques sécurité).
- Amazon SES (emails système).
- AWS Secrets Manager (SSO secrets).
- CloudWatch / OpenSearch (monitoring).

---

## 9. Contraintes

- Conformité RGPD (export et suppression utilisateur).
- Audit complet requis pour alignement ISO 27001.
- Aucun contournement possible du middleware RBAC.

---

## 10. Tests

- Tests unitaires RBAC.
- Tests d’intégration SSO.
- Tests de montée en charge login.
- Tests sécurité (brute force, injection).

Couverture minimale cible : 80% sur le module.