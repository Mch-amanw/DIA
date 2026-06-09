## Fonctionnalité : Isolation multi-tenant

### 1. Objectif métier
Garantir une isolation stricte et sécurisée des données et des traitements entre les tenants de la plateforme DIAg, tout en supportant trois niveaux d’isolation (shared, base dédiée, infrastructure dédiée) sans impact sur les fonctionnalités métier.

Cette fonctionnalité constitue un socle critique de confiance, de conformité (RGPD), de sécurité et de contractualisation commerciale (offres différenciées selon niveau d’isolation).

---

### 2. Périmètre

La fonctionnalité couvre :
- L’application du mode d’isolation choisi pour chaque tenant.
- La séparation stricte des données entre tenants.
- La transparence du mode d’isolation pour les modules métier (Référentiel, Évaluations, Benchmark, Reporting, IA, etc.).
- Le respect des règles d’accès utilisateur dans un contexte multi-tenant.

Hors périmètre :
- Définition des offres commerciales.
- Paramétrage détaillé de l’infrastructure AWS (traité dans l’infrastructure globale).

---

### 3. Modes d’isolation supportés

#### 3.1 Shared Tenant
- Base de données partagée.
- Isolation logique via `tenant_id`.
- Toutes les données sont cloisonnées par tenant.

#### 3.2 Dedicated Database
- Base PostgreSQL dédiée par tenant.
- Isolation physique au niveau base de données.
- Aucun partage de schéma ou de données.

#### 3.3 Dedicated Infrastructure
- Infrastructure AWS dédiée (compute, base, stockage).
- Isolation complète des ressources.
- Instance applicative indépendante.

---

### 4. Règles de gestion

1. Chaque tenant doit être associé à un unique mode d’isolation.
2. Le mode d’isolation ne doit pas modifier les fonctionnalités visibles par l’utilisateur.
3. Aucun utilisateur ne peut accéder aux données d’un autre tenant.
4. Les exports (PDF, CSV, API) doivent strictement respecter le périmètre du tenant actif.
5. Les traitements asynchrones (queues, génération de rapport, IA) doivent préserver le contexte tenant.
6. Le benchmark n’utilise que des données anonymisées et agrégées, sans jamais exposer d’identifiant tenant.
7. Le changement de tenant actif par un utilisateur multi-tenant doit être explicite et sécurisé.
8. La suppression d’un tenant entraîne la suppression ou anonymisation complète de ses données conformément au RGPD.

---

### 5. Critères d’acceptation

- ✅ Une requête API exécutée dans le contexte d’un tenant ne retourne jamais de données appartenant à un autre tenant.
- ✅ Les tests automatisés détectent toute requête non scoppée par tenant.
- ✅ Les trois modes d’isolation permettent l’exécution identique d’un même scénario métier (ex : création d’évaluation, génération de rapport).
- ✅ Les traitements asynchrones conservent correctement le `tenant_id`.
- ✅ Les utilisateurs multi-tenant doivent sélectionner explicitement leur contexte actif.
- ✅ La suppression d’un tenant supprime toutes ses données sans impact sur les autres tenants.

---

### 6. Dépendances fonctionnelles

- Module Authentification (gestion des utilisateurs multi-tenant).
- Module Administration (création et configuration des tenants).
- Tous les modules métier dépendants du contexte tenant.

Cette fonctionnalité est transversale et conditionne l’ensemble du fonctionnement de la plateforme.