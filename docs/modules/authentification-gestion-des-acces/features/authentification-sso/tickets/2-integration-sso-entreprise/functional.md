## Ticket : Intégration SSO entreprise

### 1. Objectif
Permettre l’intégration SSO (Single Sign-On) pour les tenants entreprise via :
- Azure AD / Entra ID
- Google Workspace
- Okta
- SAML 2.0 générique

Ce ticket couvre la configuration technique des providers OAuth2 / OpenID Connect et SAML, ainsi que le mapping des attributs utilisateurs et le provisioning automatique (Just-In-Time).

---

### 2. Périmètre fonctionnel

Inclus :
- Configuration SSO au niveau tenant.
- Support OAuth2 / OpenID Connect (Azure AD, Google, Okta).
- Support SAML 2.0 générique.
- Mapping des attributs utilisateurs (email, prénom, nom, rôle si autorisé).
- Provisioning automatique JIT.
- Association automatique de l’utilisateur au tenant.
- Option SSO exclusif (désactivation login mot de passe).
- Journalisation complète des connexions SSO.

Exclus :
- Gestion avancée des rôles (hors mapping simple si activé).
- Synchronisation continue des utilisateurs (SCIM non inclus dans ce ticket).

---

### 3. Configuration SSO par tenant

Chaque tenant peut :
- Activer ou désactiver un ou plusieurs providers.
- Configurer ses paramètres propres (client ID, metadata URL, certificats, etc.).
- Activer le mode SSO exclusif.

Règles :
- La configuration est isolée par tenant.
- Aucun impact sur les autres tenants.
- Un tenant peut activer plusieurs providers simultanément.

---

### 4. Mapping des attributs

Lors de l’authentification SSO, les attributs reçus sont mappés vers les champs utilisateur DIAg :

Attributs minimum requis :
- Email (obligatoire)
- Prénom
- Nom

Mapping configurable par tenant :
- Correspondance des claims OIDC ou attributs SAML.
- Option de mapping du rôle (si activée).

Règles :
- L’email est l’identifiant principal.
- Si un utilisateur existe déjà dans le tenant, il est réutilisé.
- Sinon, création automatique (JIT provisioning).
- Le compte créé est en statut "actif".

---

### 5. Provisioning Just-In-Time (JIT)

Si l’utilisateur authentifié via SSO n’existe pas :
1. Vérification que le domaine ou la configuration autorise l’accès.
2. Création automatique du compte.
3. Attribution du rôle par défaut du tenant ou via mapping.
4. Association au tenant.

Contraintes :
- Aucun accès inter-tenant.
- Impossible de créer un utilisateur dans un tenant non configuré.

---

### 6. Mode SSO exclusif

Si activé au niveau tenant :
- Connexion email / mot de passe désactivée.
- Réinitialisation mot de passe désactivée.
- Les utilisateurs doivent passer exclusivement par le provider SSO configuré.

Exception :
- Les Super Administrateurs plateforme ne sont pas bloqués par cette règle.

---

### 7. Journalisation & traçabilité

Pour chaque tentative SSO :
- Provider utilisé
- Succès / échec
- Date/heure
- IP
- User agent
- Tenant

Les erreurs de validation (signature invalide, token expiré, issuer incorrect) sont journalisées.

---

### 8. Contraintes de sécurité

- Vérification stricte des signatures et certificats.
- Validation issuer et audience.
- Protection CSRF via paramètre state.
- Refus de tout token invalide ou non signé.
- Isolation stricte multi-tenant.

---

### 9. Internationalisation

- Les messages d’erreur liés au SSO sont traduits (FR/EN).
- Les écrans de redirection respectent la langue préférée si disponible.