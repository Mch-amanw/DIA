# Spécification Technique

## 1. Architecture générale

### 1.1 Type d’architecture
- SaaS multi-tenant.
- Monolithe modulaire Laravel (API REST).
- Frontend React + TypeScript.
- API documentée via OpenAPI.
- Infrastructure conteneurisée.

### 1.2 Modules applicatifs
- Authentification
- Gestion organisations
- Référentiel
- Évaluations
- Benchmark
- Reporting
- IA
- Administration

Traitements asynchrones via SQS et Laravel Queues.

---

## 2. Multi-tenancy

### 2.1 Modes supportés
1. Shared Tenant (base partagée, isolation logique via tenant_id).
2. Dedicated Database (PostgreSQL dédiée).
3. Dedicated Infrastructure (AWS dédiée).

Objectif : permettre ces trois niveaux sans modification applicative majeure.

---

## 3. Stack technique

### 3.1 Frontend
- React + TypeScript
- Distribution via CloudFront

### 3.2 Backend
- Laravel API
- Déploiement sur AWS ECS Fargate

### 3.3 Base de données
- Amazon RDS PostgreSQL Multi-AZ

### 3.4 Stockage documentaire
- Amazon S3

### 3.5 Messaging
- Amazon SQS
- Laravel Queues

### 3.6 Emails
- Amazon SES

### 3.7 Secrets
- AWS Secrets Manager

### 3.8 Logs et recherche
- CloudWatch
- OpenSearch

### 3.9 Sauvegardes
- AWS Backup

---

## 4. Environnements & CI/CD
- Environnements : Development, Staging, Production
- CI/CD via GitHub Actions
- Déploiement automatisé sur AWS
- Contrôles qualité et sécurité intégrés au pipeline

---

## 5. Sécurité & conformité

### 5.1 Conformité
- RGPD
- Alignement objectif ISO 27001

### 5.2 Protection des données
- Chiffrement au repos et en transit
- Anonymisation des données envoyées aux LLM
- Filtrage automatique des données personnelles
- Droit à l’oubli
- Gestion des consentements

### 5.3 Authentification
- Email / mot de passe
- MFA
- SSO : Azure AD / Entra ID, Google Workspace, Okta, SAML 2.0

### 5.4 Audit
- Journalisation complète
- Audit trail des actions utilisateurs
- Historique des décisions IA
- Export CSV et PDF

---

## 6. IA & abstraction LLM

### 6.1 Providers supportés
- Azure OpenAI (principal)
- Mistral
- Anthropic
- OpenAI

### 6.2 Couche d’abstraction
- Abstraction obligatoire.
- Possibilité de changer de fournisseur sans modification métier.

### 6.3 Journalisation IA
- Provider
- Version modèle
- Prompts
- Réponse
- Horodatage
- Coût estimé

---

## 7. Performance & SLA

### 7.1 SLA cible
- 99,9 %

### 7.2 Objectifs de performance
- Dashboard < 2 secondes
- Questionnaire < 1 seconde
- Rapport standard < 30 secondes
- Rapport enrichi IA < 60 secondes

### 7.3 Monitoring
- CloudWatch
- OpenSearch
- Évolution possible vers Datadog

---

## 8. Internationalisation technique
- Architecture i18n prête.
- Contenus traduisibles (UI + référentiel).
- Génération PDF multi-langue.

---