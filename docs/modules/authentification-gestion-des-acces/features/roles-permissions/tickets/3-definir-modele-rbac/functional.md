## Ticket : Définir le modèle RBAC (Role-Based Access Control)

### 1. Objectif du ticket
Mettre en place le modèle RBAC standard de la plateforme DIAg afin de :
- Implémenter les rôles standards définis au niveau produit.
- Structurer un système de permissions granulaires par module et par action.
- Permettre l’attribution de rôles aux utilisateurs dans un contexte multi-tenant.
- Garantir que chaque action métier soit protégée par une permission explicite.

Ce ticket pose les fondations du contrôle d’accès pour l’ensemble des modules de la plateforme.

---

### 2. Rôles standards à implémenter
Les rôles suivants doivent être définis au niveau plateforme :

- **Super Administrateur plateforme** (scope global)
- **Administrateur organisation** (scope tenant)
- **Consultant** (scope tenant, multi-organisations possible)
- **Évaluateur** (scope tenant)
- **Lecteur / Direction** (scope tenant)

Caractéristiques :
- Les rôles standards sont non supprimables.
- Ils sont définis de manière centralisée.
- Chaque rôle correspond à un ensemble prédéfini de permissions.

---

### 3. Modèle de permissions
Les permissions doivent être granulaires et structurées selon :

#### 3.1 Dimensions
- **Module** :
  - Authentification
  - Organisations
  - Référentiel
  - Évaluations
  - Benchmark
  - Reporting
  - IA
  - Administration

- **Action** :
  - lecture
  - création
  - modification
  - validation
  - suppression
  - export

Chaque permission correspond à une combinaison (module + action).

#### 3.2 Principe de moindre privilège
- Aucun rôle ne dispose implicitement de toutes les permissions (sauf Super Administrateur plateforme).
- Toute action exposée par l’API doit être associée à une permission.

---

### 4. Attribution des rôles
- Un utilisateur peut avoir un ou plusieurs rôles.
- Les rôles sont attribués dans le périmètre d’un tenant.
- Un utilisateur multi-organisations peut avoir des rôles différents selon le tenant.
- Les rôles plateforme (ex : Super Administrateur) ne sont pas limités à un tenant.

La modification des rôles doit être possible uniquement par des utilisateurs disposant des droits adéquats.

---

### 5. Règles de gestion
- Toute requête API doit être soumise à une vérification d’authentification et de permission.
- En cas d’absence de permission, l’accès est refusé.
- La suppression d’un utilisateur entraîne la perte immédiate de ses rôles.
- Un utilisateur suspendu ne doit plus bénéficier de ses permissions.
- Les modifications de rôles et permissions doivent être traçables via le module Audit.

---

### 6. Contraintes
- Compatible multi-tenant (shared DB, DB dédiée, infra dédiée).
- Cohérent avec la séparation plateforme vs organisation.
- Conçu pour permettre une évolution future vers des rôles personnalisables (hors périmètre immédiat).

Ce ticket ne couvre pas l’interface d’administration avancée des permissions (évolution future), mais établit la structure et les règles nécessaires à leur gestion.