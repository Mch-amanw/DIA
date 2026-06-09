## Ticket : Intégration SSO entreprise – Spécification technique

### 1. Modèle de données

Table `sso_configurations` (par tenant) :
- id
- tenant_id
- provider (azure, google, okta, saml)
- protocol (oidc, saml)
- client_id
- client_secret (référence AWS Secrets Manager)
- metadata_url (OIDC discovery ou SAML metadata)
- certificate (pour SAML si nécessaire)
- attribute_mapping (JSON)
- default_role_id (nullable)
- sso_exclusive (bool)
- is_active (bool)
- created_at
- updated_at

Contraintes :
- Index sur tenant_id.
- Un provider unique par tenant.

Secrets sensibles stockés dans AWS Secrets Manager.

---

### 2. Providers supportés

#### 2.1 OAuth2 / OpenID Connect
- Azure AD / Entra ID
- Google Workspace
- Okta

Utilisation d’un client OIDC compatible Laravel.
Flux :
1. redirectToProvider()
2. Callback handleCallback()
3. Vérification state (anti-CSRF)
4. Vérification id_token (signature, issuer, audience, expiration)
5. Extraction claims

Validation obligatoire :
- iss
- aud
- exp
- signature (clé publique via JWKS ou metadata)

---

#### 2.2 SAML 2.0
- Utilisation d’une librairie SAML compatible Laravel.

Flux :
1. Redirection vers IdP.
2. Réception assertion SAML.
3. Vérification signature XML.
4. Vérification audience.
5. Vérification conditions (NotBefore / NotOnOrAfter).

Certificats validés via metadata_url ou certificate fourni.

---

### 3. Mapping des attributs

Champ `attribute_mapping` (JSON) :
Exemple :
{
  "email": "claim_email",
  "firstname": "given_name",
  "lastname": "family_name",
  "role": "role_claim"
}

Traitement :
- Extraction dynamique selon mapping.
- Email obligatoire.
- Normalisation email (lowercase).

---

### 4. Provisioning JIT

Dans `handleCallback()` :
1. Résolution tenant (TenantResolutionMiddleware).
2. Récupération configuration SSO active.
3. Extraction attributs.
4. Recherche user par email + tenant_id.
5. Si inexistant → création user :
   - password_hash = null
   - mfa_enabled = false
   - status = actif
6. Attribution rôle :
   - default_role_id ou mapping si autorisé.
7. Génération token via Sanctum ou JWT.

Toutes les opérations dans transaction DB.

---

### 5. Mode SSO exclusif

Dans AuthController@login :
- Vérifier si tenant.sso_exclusive = true.
- Si oui → refuser login password sauf rôle Super Admin.

Dans forgotPassword/resetPassword :
- Bloquer si SSO exclusif.

---

### 6. Multi-tenancy

- Filtrage systématique par tenant_id.
- Aucune configuration SSO globale.
- Compatible shared DB / DB dédiée.

Tenant résolu via :
- Domaine email.
- Sous-domaine.
- Paramètre URL.

---

### 7. Audit

Insertion dans `login_audit_logs` :
- user_id (nullable si échec pré-auth)
- tenant_id
- auth_type = 'sso'
- provider
- status
- ip_address
- user_agent
- created_at

Logs applicatifs vers CloudWatch.

---

### 8. Sécurité

- TLS obligatoire.
- Vérification stricte des signatures.
- Validation issuer/audience configurée par tenant.
- Protection CSRF via param state stocké en session.
- Rotation automatique des clés JWKS.
- Timeout et gestion erreurs explicites.

---

### 9. Performance

- Mise en cache temporaire des metadata OIDC/SAML.
- Timeout externe configurable.
- Objectif login < 500 ms hors latence provider.

---

### 10. Tests

- Tests unitaires mapping attributs.
- Tests validation signature invalide.
- Tests multi-tenant isolation.
- Tests provisioning JIT.
- Tests SSO exclusif.

Couverture cible ≥ 80% sur ce ticket.