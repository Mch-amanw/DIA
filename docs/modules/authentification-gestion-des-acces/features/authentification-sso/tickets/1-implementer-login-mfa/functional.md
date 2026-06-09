## Ticket : Implémenter login + MFA

### 1. Objectif
Mettre en œuvre le mécanisme d’authentification par email/mot de passe avec prise en charge du MFA (TOTP), incluant la gestion sécurisée des sessions, conformément aux règles de sécurité et de multi‑tenancy définies pour DIAg.

Ce ticket couvre :
- Le login email/mot de passe.
- Le déclenchement et la validation du MFA si requis.
- La gestion des tentatives échouées et du blocage temporaire.
- La création et la gestion sécurisée des sessions.
- La journalisation des événements d’authentification.

---

### 2. Périmètre fonctionnel

#### Inclus
- Authentification via email/mot de passe.
- Vérification du statut utilisateur (actif requis).
- Application de la politique de sécurité (blocage après X tentatives).
- Déclenchement du MFA si :
  - activé au niveau utilisateur, ou
  - obligatoire au niveau tenant, ou
  - requis pour un rôle sensible.
- Vérification du code OTP (TOTP).
- Création de session sécurisée après authentification complète.
- Journalisation des tentatives (succès / échec) pour :
  - mot de passe
  - MFA
- Gestion du logout.

#### Exclus
- Réinitialisation de mot de passe.
- Configuration SSO.
- Gestion détaillée des rôles et permissions (hors vérification d’accès post-login).

---

### 3. Parcours utilisateur

#### 3.1 Login sans MFA
1. L’utilisateur saisit email + mot de passe.
2. Le système vérifie :
   - l’existence du compte dans le tenant,
   - le statut "actif",
   - la validité du mot de passe.
3. Si valide et MFA non requis → session créée.
4. L’utilisateur est authentifié.

#### 3.2 Login avec MFA requis
1. L’utilisateur saisit email + mot de passe.
2. Les identifiants sont validés.
3. Si MFA requis → affichage de l’écran de saisie OTP.
4. L’utilisateur saisit le code TOTP.
5. Si valide → session créée.
6. Si invalide → refus d’accès et journalisation.

La session n’est créée qu’après validation complète (mot de passe + MFA si requis).

---

### 4. Règles de gestion

- L’email est unique au sein du tenant.
- L’utilisateur doit être en statut "actif".
- Blocage temporaire après X tentatives échouées (configurable au niveau plateforme/tenant).
- Le MFA est exigé si activé ou imposé.
- Toute tentative échouée est journalisée.
- Les événements suivants sont auditables :
  - Succès login password
  - Échec login password
  - Succès MFA
  - Échec MFA
  - Logout

---

### 5. Sécurité

- Aucun accès inter-tenant possible.
- Les messages d’erreur ne doivent pas révéler si l’email existe.
- Protection contre brute force.
- Sessions sécurisées (cookie HTTPOnly + Secure).
- Expiration configurable des sessions.
- Déconnexion invalide le token immédiatement.

---

### 6. Journalisation
Pour chaque tentative :
- user_id (si résolu)
- tenant_id
- type (password / mfa)
- succès / échec
- date/heure
- adresse IP
- user agent

Les logs doivent être exploitables pour audit et supervision sécurité.

---

### 7. Internationalisation
- Messages d’erreur et d’interface disponibles en FR/EN.
- Les messages respectent la langue préférée de l’utilisateur si connue.