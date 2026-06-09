## Spécification technique : Authentification & SSO

### 1. Composants backend (Laravel)

#### 1.1 Contrôleurs
- AuthController
  - login()
  - logout()
  - forgotPassword()
  - resetPassword()
- MFAController
  - enable()
  - verify()
  - disable()
- SSOController
  - redirectToProvider()
  - handleCallback()

#### 1.2 Middleware
- AuthenticateMiddleware
- TenantResolutionMiddleware
- RBACMiddleware
- RateLimitMiddleware

---

### 2. Modèle de données impacté

Table `users` :
- password_hash (nullable si SSO-only)
- mfa_enabled (bool)
- mfa_secret (chiffré)
- status

Table `login_audit_logs` :
- user_id
- tenant_id
- auth_type (password, mfa, sso)
- ip_address
- user_agent
- status
- created_at

Table supplémentaire possible :
- sso_configurations
  - tenant_id
  - provider
  - client_id
  - metadata_url
  - certificate
  - sso_exclusive (bool)

Secrets stockés dans AWS Secrets Manager.

---

### 3. Authentification Email / Mot de passe
- Hashage via bcrypt ou argon2 (Laravel natif).
- Rate limiting via middleware Laravel.
- Blocage après X tentatives (configurable).
- Tokens via Laravel Sanctum ou JWT sécurisé.
- Expiration configurable.

---

### 4. MFA
- Implémentation TOTP (RFC 6238).
- Génération de secret unique.
- Secret chiffré en base.
- Vérification serveur.
- Option codes de récupération stockés hashés.

---

### 5. SSO

#### 5.1 Protocoles
- OAuth2 / OpenID Connect (Azure AD, Google, Okta).
- SAML 2.0 générique.

#### 5.2 Flux
1. Redirection vers provider.
2. Authentification externe.
3. Callback sécurisé.
4. Vérification signature.
5. Mapping attributs.
6. Création ou récupération utilisateur.
7. Génération token interne.

#### 5.3 Sécurité
- Validation stricte des certificats.
- Vérification audience et issuer.
- Protection CSRF via state param.
- Rotation possible des certificats.

---

### 6. Multi-tenancy
- Résolution du tenant avant authentification complète.
- Filtrage systématique par tenant_id.
- Compatible shared DB, DB dédiée, infra dédiée sans modification métier.

---

### 7. Performance & Scalabilité
- Login < 500 ms hors SSO externe.
- Middleware permission < 50 ms.
- Scalabilité horizontale via ECS Fargate.

---

### 8. Logs & Monitoring
- Logs vers CloudWatch.
- Indexation possible OpenSearch.
- Audit trail write-only.
- Alerting possible sur tentatives suspectes.

---

### 9. Contraintes
- RGPD : export et suppression utilisateur.
- Journalisation obligatoire.
- Aucun contournement possible du middleware RBAC.
- TLS obligatoire.

---

### 10. Tests
- Tests unitaires login/password.
- Tests MFA (validation OTP).
- Tests SSO sandbox providers.
- Tests sécurité (brute force, replay, signature invalide).
- Tests multi-tenant isolation.

Couverture cible ≥ 80% sur la fonctionnalité.