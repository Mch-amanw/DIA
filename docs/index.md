# DIAg — Guide d’exécution PRISM

## Contexte

DIAg est une plateforme SaaS multi-tenant d’évaluation de la maturité IA des entreprises.  
Le cœur métier repose sur un référentiel versionné, un moteur de scoring explicable, un workflow de validation et la génération de livrables (dashboard, radar, PDF, roadmap), enrichis par des capacités IA.  

L’architecture cible est un monolithe modulaire Laravel (API REST) avec frontend React + TypeScript, déployé sur AWS (ECS Fargate, RDS PostgreSQL, S3, SQS, SES).  

Ce document définit **l’ordre recommandé d’implémentation par modules**, en respectant les dépendances techniques, les contraintes multi-tenant, les exigences de conformité et la logique métier structurante.

---

## Comment lire cette documentation

- L’ordre proposé est **par module complet**, et non par fonctionnalité.
- Chaque module est accompagné :
  - d’un **objectif**
  - de ses **prérequis**
  - de ses **livrables clés**
  - des liens vers ses spécifications :
    - `docs/modules/{slug}/functional.md`
    - `docs/modules/{slug}/technical.md`
- Les modules transverses sont positionnés stratégiquement pour sécuriser le socle technique et réglementaire.
- L’objectif est d’obtenir rapidement un **MVP cohérent**, puis d’activer les briques avancées (benchmark, IA, reporting enrichi).

---

## Ordre recommandé d’implémentation

| # | Module | Chemin | Pourquoi cet ordre |
|---|--------|--------|-------------------|
| 1 | Gestion des organisations & Multi-tenancy | `docs/modules/gestion-des-organisations-multi-tenancy/` | Socle structurel. Conditionne l’isolation des données, la stratégie tenant_id, et toute la modélisation future. |
| 2 | Authentification & Gestion des accès | `docs/modules/authentification-gestion-des-acces/` | Nécessaire pour sécuriser tous les accès. Dépend du tenant. |
| 3 | Sécurité, Conformité & Audit | `docs/modules/securite-conformite-audit/` | Module transverse. Doit être intégré tôt pour journaliser tous les modules suivants et respecter RGPD. |
| 4 | Internationalisation | `docs/modules/internationalisation/` | Impacte la structure des tables (traductions), le référentiel et les futurs rapports. À poser avant le référentiel. |
| 5 | Référentiel de maturité | `docs/modules/referentiel-de-maturite/` | Cœur méthodologique. Structure les évaluations, le scoring, le reporting et le benchmark. |
| 6 | Évaluations & Workflow | `docs/modules/evaluations-workflow/` | Cœur opérationnel. Dépend du référentiel, auth, organisations et audit. |
| 7 | Scoring & Benchmark | `docs/modules/scoring-benchmark/` | Moteur analytique. Dépend des évaluations et du référentiel versionné. |
| 8 | Reporting & Livrables | `docs/modules/reporting-livrables/` | Exploite les scores validés. Dépend du scoring, i18n et S3. |
| 9 | Gestion documentaire | `docs/modules/gestion-documentaire/` | Module complémentaire, utile pour enrichissement IA et traçabilité documentaire. |
| 10 | IA & Recommandations | `docs/modules/ia-recommandations/` | Dernier niveau de complexité (abstraction LLM, journalisation IA, conformité). Dépend de presque tous les modules précédents. |

---

# Détail par module

---

## 1. Gestion des organisations & Multi-tenancy

**Spécifications :**
- `docs/modules/gestion-des-organisations-multi-tenancy/functional.md`
- `docs/modules/gestion-des-organisations-multi-tenancy/technical.md`

### Objectif
Mettre en place le socle multi-tenant compatible :
- Shared DB
- DB dédiée
- Infrastructure dédiée

### Prérequis
Aucun (module racine).

### Livrables clés
- Table `tenants`
- Table `organizations`
- Middleware de résolution du tenant
- Global Scopes Eloquent
- Stratégie d’isolation testée
- Indexation systématique `tenant_id`

---

## 2. Authentification & Gestion des accès

**Spécifications :**
- `docs/modules/authentification-gestion-des-acces/functional.md`
- `docs/modules/authentification-gestion-des-acces/technical.md`

### Objectif
Sécuriser l’accès et implémenter RBAC tenant-aware.

### Prérequis
- Module Multi-tenancy opérationnel.

### Livrables clés
- Auth email/password
- MFA (TOTP)
- Base RBAC (roles, permissions)
- Middleware d’autorisation
- Journalisation des connexions

---

## 3. Sécurité, Conformité & Audit

**Spécifications :**
- `docs/modules/securite-conformite-audit/functional.md`
- `docs/modules/securite-conformite-audit/technical.md`

### Objectif
Garantir RGPD, audit trail et traçabilité complète.

### Prérequis
- Authentification
- Multi-tenancy

### Livrables clés
- `audit_logs`
- `consents`
- `gdpr_requests`
- `ai_execution_logs`
- Observers et Events globaux
- Export CSV/PDF des logs

---

## 4. Internationalisation

**Spécifications :**
- `docs/modules/internationalisation/functional.md`
- `docs/modules/internationalisation/technical.md`

### Objectif
Permettre un référentiel et des livrables multi-langues.

### Prérequis
- Multi-tenancy
- Auth (langue utilisateur)

### Livrables clés
- Table `languages`
- Pattern translation tables
- Middleware langue active
- Gestion fallback

---

## 5. Référentiel de maturité

**Spécifications :**
- `docs/modules/referentiel-de-maturite/functional.md`
- `docs/modules/referentiel-de-maturite/technical.md`

### Objectif
Créer le moteur méthodologique versionné.

### Prérequis
- Internationalisation
- Auth (rôles Super Admin)
- Audit

### Livrables clés
- Versioning référentiel
- Dimensions / sous-dimensions / questions
- Pondérations
- Niveaux de maturité
- Recommandations
- Verrouillage version active

---

## 6. Évaluations & Workflow

**Spécifications :**
- `docs/modules/evaluations-workflow/functional.md`
- `docs/modules/evaluations-workflow/technical.md`

### Objectif
Permettre la création et la gestion complète des diagnostics.

### Prérequis
- Référentiel actif
- Organisations
- Auth & RBAC
- Audit

### Livrables clés
- Création évaluation
- Snapshot logique référentiel
- Réponses individuelles/collaboratives
- Machine à états
- Historique transitions

---

## 7. Scoring & Benchmark

**Spécifications :**
- `docs/modules/scoring-benchmark/functional.md`
- `docs/modules/scoring-benchmark/technical.md`

### Objectif
Calculer des scores explicables et produire des agrégats anonymisés.

### Prérequis
- Évaluations
- Référentiel versionné

### Livrables clés
- ScoreComputationService
- Snapshot scores validés
- Agrégation collaborative
- BenchmarkAggregate anonymisé
- Tests de performance (<1s recalcul)

---

## 8. Reporting & Livrables

**Spécifications :**
- `docs/modules/reporting-livrables/functional.md`
- `docs/modules/reporting-livrables/technical.md`

### Objectif
Transformer les scores en livrables décisionnels.

### Prérequis
- Scoring validé
- Internationalisation
- S3 opérationnel

### Livrables clés
- Dashboard React
- Radar chart
- Génération PDF asynchrone (SQS)
- Snapshot figé des rapports
- Roadmap priorisée

---

## 9. Gestion documentaire

**Spécifications :**
- `docs/modules/gestion-documentaire/functional.md`
- `docs/modules/gestion-documentaire/technical.md`

### Objectif
Permettre le stockage sécurisé des documents par tenant.

### Prérequis
- Multi-tenancy
- Auth
- S3

### Livrables clés
- Upload sécurisé
- Métadonnées PostgreSQL
- URLs signées
- Soft delete
- Convention de nommage S3 par tenant

---

## 10. IA & Recommandations

**Spécifications :**
- `docs/modules/ia-recommandations/functional.md`
- `docs/modules/ia-recommandations/technical.md`

### Objectif
Fournir des recommandations personnalisées via abstraction LLM.

### Prérequis
- Scoring
- Reporting
- Gestion documentaire
- Sécurité & Audit (logs IA obligatoires)

### Livrables clés
- Couche d’abstraction multi-provider
- Gestion des prompts versionnés
- Journalisation complète IA
- Filtrage données personnelles
- Gestion des coûts estimés

---

# Points d’attention transverses

## 1. Multi-tenancy strict
- `tenant_id` obligatoire partout.
- Tests automatiques d’isolation.
- Interdiction requêtes globales non filtrées.

## 2. Versioning immuable
- Une évaluation validée ne doit jamais être impactée.
- Snapshots obligatoires (scores, rapport).

## 3. Performance
- Pré-calcul des scores validés.
- PDF en asynchrone.
- Index PostgreSQL adaptés.

## 4. Sécurité
- Chiffrement au repos et en transit.
- Anonymisation stricte pour benchmark.
- Chiffrement des prompts IA si nécessaire.

## 5. Observabilité
- Logs CloudWatch structurés.
- Corrélation `tenant_id` + `request_id`.

---

# Prochaines étapes suggérées

1. ✅ Valider cet ordre avec l’équipe technique et produit.
2. ✅ Créer les repositories modulaires Laravel structurés dès le départ.
3. ✅ Mettre en place CI/CD (GitHub Actions + environnements AWS).
4. ✅ Implémenter Modules 1 à 3 avant toute logique métier.
5. ✅ Définir un référentiel minimal V1 pour accélérer le développement des évaluations.
6. ✅ Planifier des tests d’isolation multi-tenant dès les premiers sprints.
7. ✅ Définir une stratégie de tests automatisés (unitaires + intégration + performance).

---

Ce plan permet de construire DIAg sur un socle sécurisé, multi-tenant et conforme, avant d’activer progressivement la valeur métier (scoring, reporting, benchmark, IA).