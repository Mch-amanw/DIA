# Ticket : CRUD organisations et filiales

## 1. Objectif du ticket

Implémenter la gestion complète (CRUD) des organisations, groupes, filiales et divisions au sein d’un tenant, ainsi que le rattachement et le détachement des utilisateurs à ces entités.

Ce ticket couvre :
- La création, modification et archivage des entités organisationnelles.
- La visualisation de la hiérarchie.
- La gestion du rattachement des utilisateurs aux entités.
- Le respect strict des règles multi-tenant et des contraintes hiérarchiques.

---

## 2. Création d’une entité organisationnelle

### 2.1 Acteurs autorisés
- Administrateur organisation (du tenant courant)
- Super Administrateur plateforme

### 2.2 Données saisies
Lors de la création, les champs suivants sont requis :
- Nom
- Type (holding, filiale, division, entité opérationnelle)
- Pays (code ISO)
- Entité parente (optionnelle si création de la racine)
- Métadonnées (JSON structuré optionnel)

### 2.3 Règles métier
- Une seule entité racine autorisée par tenant.
- Toute entité doit appartenir au tenant actif.
- Une entité parente doit appartenir au même tenant.
- Interdiction de créer une hiérarchie avec cycle.
- L’entité est active par défaut (`non archivée`).

---

## 3. Modification d’une entité

Un administrateur peut :
- Modifier le nom.
- Modifier le type.
- Modifier le pays.
- Modifier les métadonnées.
- Changer l’entité parente.

### Règles spécifiques
- Le changement de parent ne doit pas créer de cycle.
- L’entité doit rester dans le même tenant.
- Toute modification structurelle est journalisée.

---

## 4. Archivage d’une entité

### 4.1 Archivage logique
- Suppression physique interdite.
- Passage du statut `is_archived = true`.

### 4.2 Effets de l’archivage
- L’entité ne peut plus être sélectionnée pour de nouvelles évaluations.
- Les évaluations existantes restent accessibles en lecture.
- L’entité reste visible dans l’arborescence (marquée comme archivée).

---

## 5. Visualisation des organisations

### 5.1 Vue arborescente
- Affichage hiérarchique complet du tenant.
- Navigation parent → enfants.
- Indication visuelle des entités archivées.

### 5.2 Vue liste
- Liste filtrable par :
  - Type
  - Pays
  - Statut (active / archivée)
- Pagination si nécessaire.

### 5.3 Périmètre
- Un utilisateur ne voit que les entités du tenant actif.
- Les restrictions supplémentaires par rattachement sont appliquées selon le rôle.

---

## 6. Rattachement des utilisateurs

### 6.1 Principes
Un utilisateur peut être rattaché à :
- Une ou plusieurs entités du même tenant.

Le rôle reste défini au niveau tenant (Administrateur, Consultant, Évaluateur, Lecteur).

### 6.2 Règles métier
- L’utilisateur doit déjà être membre du tenant.
- Interdiction de rattacher un utilisateur à une entité d’un autre tenant.
- Le rattachement limite l’accès aux évaluations liées à cette entité.
- Un Administrateur organisation peut être rattaché implicitement à toutes les entités.

### 6.3 Détachement
- Possibilité de retirer un utilisateur d’une entité.
- Le détachement ne supprime pas son appartenance au tenant.

---

## 7. Contraintes multi-tenant

- Isolation stricte entre tenants.
- Aucun affichage ou manipulation inter-tenant possible.
- Le contexte tenant actif doit être explicite.

---

## 8. Journalisation

Chaque action doit être tracée :
- Création
- Modification
- Changement de parent
- Archivage
- Rattachement utilisateur
- Détachement utilisateur

Les logs doivent inclure :
- user_id
- tenant_id
- organization_id
- type d’action
- horodatage

---

## 9. Dépendances fonctionnelles

Dépend de :
- Authentification (gestion utilisateurs et rôles multi-tenant)

Impacte :
- Module Évaluations (rattachement obligatoire à une organisation)
- Reporting (comparaison inter-entités)
- Benchmark (agrégation uniquement au niveau tenant)