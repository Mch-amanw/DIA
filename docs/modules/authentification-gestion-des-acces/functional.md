# Module : Authentification & Gestion des accès

## 1. Rôle du module
Le module **Authentification & Gestion des accès** garantit l’accès sécurisé à la plateforme DIAg et la gestion des droits des utilisateurs dans un contexte **SaaS multi-tenant**.

Il permet :
- L’authentification des utilisateurs (email/mot de passe, MFA, SSO).
- La gestion des comptes utilisateurs.
- L’attribution de rôles et permissions granulaires.
- Le contrôle d’accès aux fonctionnalités métier (référentiel, évaluations, benchmark, reporting, IA, administration).
- La traçabilité complète des connexions et actions sensibles.

Ce module est transversal et conditionne l’accès à l’ensemble des autres modules.

---

## 2. Gestion des utilisateurs

### 2.1 Création de compte
Création possible via :
- Invitation par un Administrateur organisation.
- Invitation par un Consultant (pour ses clients).
- Création par un Super Administrateur plateforme.
- Provisionnement automatique via SSO (si activé).

Champs principaux :
- Prénom
- Nom
- Email (unique par tenant)
- Organisation (tenant)
- Rôle
- Langue préférée
- Statut (actif, invité, suspendu, supprimé)

Règles :
- Un utilisateur appartient à une organisation (tenant).
- Un consultant peut être rattaché à plusieurs organisations selon le modèle multi-tenant.
- L’email est unique au sein d’un tenant (et globalement unique si exigé par la configuration plateforme).

---

## 3. Authentification

### 3.1 Email / Mot de passe
- Authentification classique.
- Politique de mot de passe configurable (longueur minimale, complexité).
- Réinitialisation via email sécurisé.
- Protection contre le brute force.

### 3.2 MFA (Multi-Factor Authentication)
- Activation obligatoire ou optionnelle selon la politique du tenant.
- Support OTP (application d’authentification).
- MFA exigé pour rôles sensibles (ex : Super Admin).

### 3.3 SSO
Support des fournisseurs :
- Azure AD / Entra ID
- Google Workspace
- Okta
- SAML 2.0 générique

Fonctionnalités :
- Association automatique à un tenant.
- Mapping des attributs (email, nom, rôle si configuré).
- Gestion Just-In-Time provisioning.

Règles :
- Un tenant peut activer/désactiver le SSO.
- Un tenant peut imposer le SSO exclusif.

---

## 4. Rôles et permissions

### 4.1 Rôles standards
- Super Administrateur plateforme
- Administrateur organisation
- Consultant
- Évaluateur
- Lecteur / Direction

### 4.2 Permissions granulaires
Permissions configurables par :
- Module (Référentiel, Évaluations, Benchmark, Reporting, IA, Administration).
- Action (lecture, création, modification, validation, suppression, export).

Règles :
- Les permissions sont définies au niveau plateforme.
- Les rôles peuvent être personnalisables selon l’évolution produit.
- Le contrôle d’accès est vérifié sur chaque requête API.

---

## 5. Gestion des accès multi-tenant

- Isolation stricte des données entre tenants.
- Un utilisateur ne peut accéder qu’aux ressources de son organisation.
- Cas particulier : Consultant multi-clients avec accès explicitement autorisé.
- Support des trois niveaux d’isolation (shared, base dédiée, infra dédiée) sans modification fonctionnelle.

---

## 6. Audit et traçabilité

### 6.1 Audit des connexions
Journalisation de :
- Tentatives de connexion (succès / échec).
- Activation/désactivation MFA.
- Réinitialisation de mot de passe.
- Connexion SSO.
- Déconnexions.

Données enregistrées :
- Identifiant utilisateur
- Tenant
- Date/heure
- Adresse IP
- User agent
- Résultat

### 6.2 Audit des actions sensibles
- Création/suppression d’utilisateur.
- Modification des rôles.
- Changement de politique de sécurité.
- Blocage/déblocage de compte.

Exports possibles en CSV/PDF (selon droits).

---

## 7. Règles de sécurité et conformité

- Respect RGPD (droit à l’oubli, export des données utilisateur).
- Suppression ou anonymisation contrôlée.
- Chiffrement des données sensibles.
- Journalisation non modifiable (audit trail).

---

## 8. Internationalisation

- Interface d’authentification multilingue.
- Emails système (invitation, reset, MFA) traduits.

---

## 9. Dépendances

- Module Organisations (gestion des tenants).
- Module Administration (configuration des politiques de sécurité).
- Module Audit global.
- Infrastructure IAM AWS (pour secrets, configuration SSO).