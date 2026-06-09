# Fonctionnalité : Gestion des organisations & filiales

## 1. Objectif métier

Permettre à un tenant (entreprise ou cabinet de conseil) de structurer son périmètre organisationnel dans DIAg en définissant :

- Une organisation principale (racine).
- Des groupes, filiales et divisions.
- Le rattachement des utilisateurs à une ou plusieurs entités.

Cette structuration permet :
- D’associer chaque évaluation à une entité précise.
- De comparer les scores entre filiales ou divisions.
- De refléter fidèlement la structure réelle de l’entreprise.

---

## 2. Périmètre fonctionnel

### 2.1 Création d’entités organisationnelles

Un administrateur d’organisation (ou Super Administrateur plateforme) peut :

- Créer une entité organisationnelle.
- Définir :
  - Nom
  - Type (holding, filiale, division, entité opérationnelle)
  - Pays
  - Métadonnées (JSON libre structuré)
  - Entité parente (pour construire la hiérarchie)

Contraintes :
- Une seule entité racine par tenant.
- Une entité doit appartenir obligatoirement au tenant courant.
- Une entité peut avoir zéro ou plusieurs entités enfants.

---

### 2.2 Modification et archivage

Un administrateur peut :

- Modifier les informations d’une entité.
- Changer son rattachement hiérarchique (dans le même tenant).
- Archiver une entité (soft delete logique).

Règles :
- Une entité avec des évaluations existantes ne peut pas être supprimée définitivement.
- L’archivage rend l’entité non sélectionnable pour de nouvelles évaluations.
- L’historique des évaluations reste intact.

---

### 2.3 Visualisation de la hiérarchie

Le système doit permettre :

- Une vue arborescente (tree view).
- Une vue liste filtrable.
- Navigation parent → enfants.

Les utilisateurs ne peuvent visualiser que les entités de leur tenant.

---

### 2.4 Rattachement des utilisateurs

Un utilisateur peut être rattaché à :

- Une ou plusieurs entités organisationnelles.
- Avec un rôle défini au niveau du tenant (Administrateur, Consultant, Évaluateur, Lecteur).

Règles :
- Le rattachement à une entité limite le périmètre d’accès aux évaluations liées à cette entité.
- Un Administrateur organisation peut avoir accès à toutes les entités.
- Un utilisateur multi-tenant doit avoir un contexte actif explicite avant toute action.

---

### 2.5 Rattachement des évaluations

- Chaque évaluation doit obligatoirement être rattachée à une entité organisationnelle.
- Une évaluation ne peut appartenir qu’à une seule entité.
- Les comparaisons inter-entités sont autorisées uniquement au sein du même tenant.

---

## 3. Règles de gestion

- Isolation stricte : aucune entité ne peut référencer un autre tenant.
- Interdiction des cycles hiérarchiques (A parent de B, B parent de A).
- Limite configurable de profondeur hiérarchique (à définir au niveau plateforme).
- Les entités archivées restent visibles en lecture seule si elles ont un historique.
- Toute modification structurelle est journalisée (audit trail).

---

## 4. Critères d’acceptation

- ✅ Un administrateur peut créer une hiérarchie complète groupe → filiales → divisions.
- ✅ Une évaluation ne peut être créée sans sélection d’une entité valide.
- ✅ Un utilisateur rattaché uniquement à une filiale ne voit pas les données d’une autre filiale.
- ✅ Un changement de tenant actif change instantanément le périmètre visible.
- ✅ L’archivage d’une entité empêche toute nouvelle évaluation sur celle-ci.

---

## 5. Dépendances fonctionnelles

Dépend :
- Module Authentification (gestion utilisateurs & rôles multi-tenant).
- Module Évaluations (rattachement obligatoire à une entité).

Impacte :
- Reporting (comparaison inter-entités).
- Benchmark (agrégation au niveau tenant uniquement).