## Spécification technique : Isolation multi-tenant

### 1. Principes d’architecture

L’application Laravel doit fonctionner de manière identique quel que soit le mode d’isolation. La logique métier ne doit jamais dépendre explicitement du type d’isolation.

Un service central de résolution du tenant détermine :
- Le tenant actif.
- Le mode d’isolation.
- La connexion base de données à utiliser.

---

### 2. Résolution du tenant courant

#### 2.1 Sources possibles
- JWT / session utilisateur (tenant_id actif).
- Sous-domaine ou configuration dédiée (mode infra dédiée).

#### 2.2 Middleware
- Middleware Laravel obligatoire en entrée de chaque requête.
- Vérification du droit d’accès de l’utilisateur au tenant.
- Injection du tenant dans le container applicatif.
- Blocage immédiat si incohérence.

---

### 3. Mode Shared (Isolation logique)

#### 3.1 Base de données
- Colonne `tenant_id` obligatoire sur toutes les tables métier.
- Index systématique sur `tenant_id`.

#### 3.2 Eloquent Global Scope
- Global Scope automatique filtrant par `tenant_id`.
- Interdiction d’accès sans scope explicite.

#### 3.3 Sécurité
- Revue automatique des migrations pour vérifier la présence de `tenant_id`.
- Tests unitaires garantissant qu’aucune requête ne contourne le scope.

---

### 4. Mode Dedicated Database

#### 4.1 Multi-connexion Laravel
- Configuration dynamique de la connexion DB selon tenant.
- Résolution via service provider.

#### 4.2 Provisioning
- Création base PostgreSQL dédiée.
- Exécution automatique des migrations.
- Stockage des credentials dans AWS Secrets Manager.

#### 4.3 Transparence métier
- Les repositories et services métier n’ont aucune connaissance du mode DB dédiée.

---

### 5. Mode Dedicated Infrastructure

#### 5.1 Isolation
- ECS cluster dédié.
- RDS dédiée.
- S3 dédié.

#### 5.2 Configuration
- Variables d’environnement spécifiques.
- Pipeline CI/CD capable de cibler un tenant précis.

#### 5.3 Journalisation
- Logs isolés par tenant.
- Monitoring indépendant.

---

### 6. Gestion du contexte dans les traitements asynchrones

- Chaque job Laravel doit inclure explicitement le `tenant_id`.
- À l’exécution du job : réinitialisation du contexte tenant.
- Tests d’intégration sur files SQS.

---

### 7. Sécurité & conformité

- Chiffrement RDS et S3 activé.
- Secrets isolés par tenant si DB dédiée ou infra dédiée.
- Audit trail incluant `tenant_id`.
- Procédure de suppression tenant :
  - Shared : suppression filtrée par `tenant_id`.
  - DB dédiée : suppression base complète.
  - Infra dédiée : destruction infra via pipeline.

---

### 8. Performance

- Index composite possible (`tenant_id`, `created_at`).
- Monitoring spécifique du temps de réponse par tenant.
- Aucun surcoût notable en mode shared.

---

### 9. Tests

- Tests unitaires : isolation Eloquent.
- Tests d’intégration : scénario multi-tenant.
- Tests de non-régression sur changement de tenant actif.
- Tests de sécurité : tentative d’accès cross-tenant.

Cette fonctionnalité est critique et bloquante pour la mise en production.