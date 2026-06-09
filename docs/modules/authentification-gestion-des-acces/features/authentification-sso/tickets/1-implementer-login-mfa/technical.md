## Implémentation technique – Login + MFA

### 1. Endpoints API

#### POST /api/auth/login
- Input : email, password
- Output :
  - Si MFA non requis → token/session
  - Si MFA requis → statut "mfa_required" + identifiant temporaire de session

#### POST /api/auth/mfa/verify
- Input : mfa_token_id, otp_code
- Output : token/session si succès

#### POST /api/auth/logout
- Invalide la session/token courant

---

### 2. Flux backend

#### 2.1 Login (AuthController@login)
1. Résolution du tenant (TenantResolutionMiddleware).
2. Recherche user par email + tenant_id.
3. Vérification status == actif.
4. Vérification password via Hash::check().
5. Si échec → incrément compteur tentatives + log + retour erreur générique.
6. Si succès →
   - reset compteur tentatives.
   - vérifier si MFA requis.
   - Si non requis → générer token (Sanctum ou JWT).
   - Si requis → générer mfa_token temporaire stocké en base ou cache sécurisé (TTL court, ex 5 min).

---

### 3. Gestion MFA

#### 3.1 Activation
- mfa_secret généré via librairie TOTP compatible RFC 6238.
- Secret stocké chiffré (Laravel encryption).

#### 3.2 Vérification (MFAController@verify)
- Récupération du mfa_token temporaire.
- Vérification TOTP avec fenêtre de tolérance (ex : ±1 intervalle).
- Si succès → génération token final.
- Suppression mfa_token temporaire.
- Log succès.
- Si échec → log échec.

Codes OTP jamais stockés.

---

### 4. Modèle de données impacté

Table users :
- failed_login_attempts (int)
- locked_until (datetime nullable)

Table login_audit_logs :
- user_id
- tenant_id
- auth_type (password|mfa)
- ip_address
- user_agent
- status (success|failure)
- created_at

Table temporaire ou cache :
- mfa_challenges
  - id
  - user_id
  - expires_at
  - created_at

---

### 5. Sécurité

- Hash password : bcrypt ou argon2 (Laravel natif).
- Rate limiting via middleware Laravel (ThrottleRequests).
- Blocage si failed_login_attempts > seuil configuré.
- Cookies : HttpOnly, Secure, SameSite=strict.
- TLS obligatoire.
- CSRF protection si cookie-based auth.

---

### 6. Gestion des sessions

Option recommandée : Laravel Sanctum.
- Token personnel lié à user_id + tenant_id.
- Expiration configurable.
- Révocation à logout.

Les tokens doivent inclure :
- user_id
- tenant_id
- issued_at
- expiration

---

### 7. Multi-tenancy

- Filtrage systématique par tenant_id.
- Aucun accès utilisateur hors tenant.
- Compatible shared DB et DB dédiée.

---

### 8. Logs & Monitoring

- Enregistrement login_audit_logs en base.
- Logs applicatifs vers CloudWatch.
- Possibilité d’alerting sur échecs répétés.

---

### 9. Tests techniques à prévoir (implémentation)

- Test succès login sans MFA.
- Test succès login avec MFA.
- Test OTP invalide.
- Test blocage après X tentatives.
- Test isolation multi-tenant.
- Test expiration mfa_token.

Couverture cible ≥ 80% sur ce périmètre.