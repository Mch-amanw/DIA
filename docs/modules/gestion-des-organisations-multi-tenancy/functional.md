# Module : Gestion des organisations & Multi-tenancy

## 1. Rôle du module

Le module **Gestion des organisations & Multi-tenancy** est le socle structurel de la plateforme DIAg. Il permet :

- La gestion des tenants (clients de la plateforme).
- La structuration des organisations (entreprise, groupe, filiales).
- L’isolation des données selon le niveau d’offre (shared, base dédiée, infrastructure dédiée).
- La séparation stricte des données entre clients.

Ce module conditionne l’ensemble des autres modules (évaluations, référentiel, benchmark, reporting, IA) qui doivent tous fonctionner dans un contexte tenant isolé.

---

## 2. Gestion des tenants

### 2.1 Création d’un tenant
Un tenant représente un client distinct de la plateforme (entreprise ou cabinet de conseil).

Un tenant contient :
- Informations légales (nom, SIRET/équivalent si applicable).
- Type d’organisation (entreprise, cabinet de conseil).
- Secteur d’activité.
- Taille d’entreprise.
- Zone géographique principale.
- Langue par défaut.
- Paramètres IA (activation/désactivation).
- Mode d’isolation (shared, DB dédiée, infra dédiée).

La création peut être réalisée :
- Par un Super Administrateur plateforme.
- Via un processus d’onboarding encadré.

---

### 2.2 Paramétrage du niveau d’isolation
Chaque tenant est associé à un niveau d’isolation :

1. **Shared Tenant**  
   - Base partagée.
   - Isolation logique via `tenant_id`.

2. **Dedicated Database**  
   - Base PostgreSQL dédiée.
   - Isolation physique au niveau base.

3. **Dedicated Infrastructure**  
   - Infrastructure AWS dédiée.
   - Isolation complète (compute + base + stockage).

Le choix d’isolation est défini lors de la création et ne doit pas impacter le fonctionnement fonctionnel des autres modules.

---

## 3. Gestion des organisations internes

### 3.1 Structure organisationnelle
Un tenant peut contenir :

- Une organisation principale.
- Des filiales.
- Des entités ou divisions internes.

Structure hiérarchique :
- Groupe
  - Filiale A
  - Filiale B
    - Division B1

Chaque entité organisationnelle dispose :
- Nom.
- Type (holding, filiale, division).
- Pays.
- Métadonnées (secteur spécifique si différent du groupe).

---

### 3.2 Rattachement des évaluations
- Chaque évaluation est rattachée à une entité organisationnelle.
- Les scores peuvent être comparés entre entités d’un même tenant.
- Aucune donnée n’est visible entre tenants distincts.

---

## 4. Gestion des consultants multi-clients

Dans le cas d’un cabinet de conseil :

- Un tenant "cabinet" peut gérer plusieurs organisations clientes.
- Chaque client reste un tenant isolé.
- Le consultant peut être rattaché à plusieurs tenants selon ses droits.

Règles :
- Un utilisateur peut appartenir à plusieurs tenants.
- Le contexte actif doit toujours être explicite (tenant courant sélectionné).
- Aucun mélange de données inter-tenant n’est possible.

---

## 5. Paramètres configurables par tenant

Chaque tenant peut configurer :

- Activation/désactivation des fonctionnalités IA.
- Langue par défaut.
- Paramètres de scoring spécifiques (si autorisé).
- Référentiel personnalisé.
- Paramètres de benchmark (inclusion ou exclusion des données anonymisées).

Ces paramètres n’affectent que le tenant concerné.

---

## 6. Règles de gestion essentielles

- Isolation stricte des données entre tenants.
- Aucune requête ne peut retourner des données d’un autre tenant.
- Les exports (PDF, CSV) sont strictement limités au périmètre du tenant.
- Les données utilisées pour le benchmark sont anonymisées et agrégées.
- Suppression d’un tenant conforme RGPD (droit à l’oubli).
- Traçabilité des actions administratives sur un tenant.

---

## 7. Dépendances fonctionnelles

Ce module est transversal et dépend :
- Du module Authentification (gestion des utilisateurs multi-tenant).
- Du module Administration (création et gestion des tenants).

Il conditionne :
- Référentiel
- Évaluations
- Benchmark
- Reporting
- IA

Aucun module métier ne peut fonctionner hors du contexte tenant.