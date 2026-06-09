## Fonctionnalité : Rôles & Permissions

### 1. Objectif métier
La fonctionnalité **Rôles & Permissions** permet de contrôler finement l’accès aux fonctionnalités de la plateforme DIAg selon le rôle de l’utilisateur et son organisation (tenant). Elle garantit que chaque utilisateur dispose uniquement des droits nécessaires à son périmètre d’action, dans un contexte SaaS multi-tenant sécurisé.

Elle contribue à :
- La séparation des responsabilités.
- La protection des données sensibles.
- La conformité RGPD et l’alignement ISO 27001.
- La sécurisation des processus critiques (validation d’évaluation, gestion du référentiel, configuration IA, etc.).

---

### 2. Périmètre fonctionnel

#### 2.1 Rôles standards
Les rôles standards définis au niveau plateforme sont :
- **Super Administrateur plateforme**
- **Administrateur organisation**
- **Consultant**
- **Évaluateur**
- **Lecteur / Direction**

Ces rôles déterminent un ensemble de permissions prédéfinies.

#### 2.2 Permissions granulaires
Les permissions sont structurées selon :
- **Module** : Référentiel, Évaluations, Benchmark, Reporting, IA, Administration, Authentification.
- **Action** : lecture, création, modification, validation, suppression, export.

Exemples :
- Lire une évaluation.
- Modifier une question du référentiel.
- Valider une évaluation.
- Générer un rapport PDF.
- Exporter les logs d’audit.

Chaque requête métier est conditionnée par la vérification explicite d’une permission.

#### 2.3 Attribution des rôles
- Un utilisateur peut avoir un ou plusieurs rôles selon les règles définies.
- Les rôles sont attribués lors de la création du compte ou modifiables par un utilisateur disposant des droits nécessaires.
- Les rôles sont attribués dans le périmètre d’un tenant.

#### 2.4 Gestion multi-tenant
- Les permissions s’appliquent exclusivement aux ressources du tenant concerné.
- Un consultant peut disposer de rôles distincts selon les organisations auxquelles il est rattaché.
- Aucun accès inter-tenant n’est possible sans autorisation explicite.

#### 2.5 Séparation des périmètres
- Les rôles plateforme (ex : Super Admin) ont un périmètre global.
- Les rôles organisation sont limités au tenant.
- Les permissions critiques (ex : modification des politiques de sécurité) sont restreintes à des rôles spécifiques.

---

### 3. Règles de gestion

- Toute action API nécessite une vérification d’authentification et de permission.
- L’absence de permission entraîne un refus d’accès (erreur 403).
- La modification d’un rôle ou d’une permission est journalisée.
- Un utilisateur suspendu ou supprimé perd immédiatement ses droits.
- Les permissions ne peuvent pas être contournées via accès direct API.
- Les rôles standards ne peuvent être supprimés.

---

### 4. Critères d’acceptation

- ✅ Un utilisateur ne peut accéder qu’aux modules autorisés par son rôle.
- ✅ Une tentative d’accès non autorisée renvoie une erreur sécurisée.
- ✅ Un Administrateur organisation peut attribuer un rôle à un utilisateur de son tenant.
- ✅ Un utilisateur multi-organisations ne voit que les données du tenant sélectionné.
- ✅ Toute modification de rôle est visible dans l’audit trail.
- ✅ Les permissions sont cohérentes avec les actions exposées par l’API.

---

### 5. Hors périmètre

- Création libre de rôles personnalisés (évolution future).
- Gestion ABAC (Attribute-Based Access Control).
- Délégation temporaire automatisée de droits.