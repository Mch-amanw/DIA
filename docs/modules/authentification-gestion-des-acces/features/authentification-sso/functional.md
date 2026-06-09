## Fonctionnalité : Authentification & SSO

### 1. Objectif métier
Permettre un accès sécurisé et conforme à la plateforme DIAg via :
- Authentification email / mot de passe.
- Authentification multi‑facteur (MFA).
- Authentification fédérée via SSO (Azure AD / Entra ID, Google Workspace, Okta, SAML 2.0).

Cette fonctionnalité garantit un haut niveau de sécurité, une compatibilité avec les exigences des entreprises (notamment grands comptes) et une expérience fluide pour les utilisateurs finaux.

---

### 2. Périmètre
Inclus :
- Connexion email / mot de passe.
- Politique de mot de passe configurable.
- Réinitialisation sécurisée du mot de passe.
- Activation et vérification MFA (TOTP).
- Intégration SSO multi-providers.
- Provisioning Just-In-Time (JIT) via SSO.
- Imposition du SSO exclusif au niveau tenant.
- Gestion des erreurs d’authentification.

Exclus :
- Gestion détaillée des rôles et permissions (traitée dans une autre fonctionnalité).
- Gestion complète des utilisateurs (hors provisioning automatique lié au SSO).

---

### 3. Authentification Email / Mot de passe

#### 3.1 Règles de gestion
- L’email est unique au sein d’un tenant.
- Le mot de passe doit respecter la politique définie (longueur minimale, complexité si activée).
- Après X tentatives échouées, le compte est temporairement bloqué.
- La réinitialisation du mot de passe se fait via un lien sécurisé à usage unique avec expiration.
- L’utilisateur doit être en statut "actif" pour se connecter.

#### 3.2 Cas particuliers
- Si le tenant impose le SSO exclusif, la connexion email/mot de passe est désactivée.
- Si le MFA est obligatoire pour le rôle ou le tenant, la connexion n’est validée qu’après vérification du second facteur.

---

### 4. MFA (Multi-Factor Authentication)

#### 4.1 Mode supporté
- TOTP via application d’authentification.

#### 4.2 Règles de gestion
- Activation possible par utilisateur ou obligatoire par politique tenant.
- Requis pour les rôles sensibles (ex : Super Administrateur plateforme).
- Le secret MFA est généré lors de l’activation.
- Possibilité de codes de récupération si activé.
- Toute désactivation est auditée.

#### 4.3 Parcours utilisateur
1. Connexion email/mot de passe valide.
2. Demande du code OTP.
3. Vérification.
4. Accès accordé si code valide.

---

### 5. Authentification SSO

#### 5.1 Providers supportés
- Azure AD / Entra ID
- Google Workspace
- Okta
- SAML 2.0 générique

#### 5.2 Règles de gestion
- Activation configurable par tenant.
- Mapping configurable des attributs (email, prénom, nom, rôle si autorisé).
- Provisioning automatique JIT si l’utilisateur n’existe pas.
- Association automatique au tenant configuré.
- Vérification obligatoire des signatures et certificats.
- Possibilité d’imposer le SSO exclusif.

#### 5.3 Gestion multi-tenant
- La résolution du tenant se fait via :
  - Domaine email.
  - Paramètre d’URL dédié.
  - Configuration spécifique du provider.

- Un utilisateur SSO ne peut accéder qu’aux tenants explicitement autorisés.

---

### 6. Audit & Traçabilité

Pour chaque tentative :
- Succès / échec.
- Type (password, MFA, SSO).
- Date/heure.
- Adresse IP.
- User agent.
- Tenant associé.

Toutes les actions liées au MFA (activation, désactivation) sont journalisées.

---

### 7. Critères d’acceptation
- Un utilisateur actif peut se connecter en moins de 500 ms (hors latence provider SSO).
- Un tenant peut activer/désactiver le SSO sans impact sur les autres.
- Le MFA est exigé si configuré.
- Le provisioning JIT crée correctement l’utilisateur avec rattachement au tenant.
- Les tentatives échouées sont visibles dans l’audit.
- Aucun accès inter-tenant possible.

---

### 8. Internationalisation
- Interfaces de login multilingues (FR/EN V1).
- Emails (invitation, reset) traduits selon la langue préférée.