# Module : Gestion des organisations & Multi-tenancy – Spécification Technique

## 1. Architecture multi-tenant

Le module doit supporter les trois niveaux d’isolation définis dans la spécification technique globale :

1. Shared database (tenant_id obligatoire).
2. Dedicated PostgreSQL database.
3. Dedicated AWS infrastructure.

L’architecture applicative (Laravel) doit rester identique quel que soit le mode d’isolation.

---

## 2. Modélisation des données (mode shared)

### 2.1 Tables principales

- `tenants`
  - id (UUID)
  - name
  - type
  - sector
  - company_size
  - geography
  - isolation_mode
  - default_language
  - ia_enabled (boolean)
  - created_at / updated_at

- `organizations`
  - id (UUID)
  - tenant_id (FK)
  - parent_id (nullable, FK self)
  - name
  - type
  - country
  - metadata (JSONB)

Toutes les tables métier doivent contenir :
- `tenant_id` (indexé).

---

## 3. Isolation logique (Shared Mode)

### 3.1 Middleware Laravel
- Résolution automatique du tenant courant.
- Injection du `tenant_id` dans le contexte applicatif.
- Filtrage global via Global Scopes Eloquent.

### 3.2 Sécurité
- Interdiction de requêtes sans contrainte `tenant_id`.
- Tests automatiques vérifiant l’isolation.

---

## 4. Dedicated Database Mode

- Mapping tenant → configuration base de données dynamique.
- Résolution de connexion via service provider.
- Migration automatique lors du provisioning.
- Stratégie multi-connexion Laravel.

Aucune logique métier différente.

---

## 5. Dedicated Infrastructure Mode

- Déploiement isolé sur AWS (ECS, RDS, S3 dédiés).
- Configuration spécifique via variables d’environnement.
- Pipeline CI/CD capable de cibler une infra dédiée.

---

## 6. Gestion du contexte utilisateur

### 6.1 Utilisateur multi-tenant

Table pivot :
- `tenant_user`
  - user_id
  - tenant_id
  - role

Le JWT ou session doit inclure :
- tenant_id actif.
- rôle dans ce tenant.

Changement de tenant via endpoint sécurisé.

---

## 7. Provisioning automatisé

À la création d’un tenant :

- Création entrée en base centrale.
- Si DB dédiée : création base PostgreSQL.
- Si infra dédiée : déclenchement pipeline infra.
- Initialisation paramètres par défaut.
- Création organisation racine.

Traitements asynchrones via SQS + Laravel Queues.

---

## 8. Performance

- Index obligatoire sur `tenant_id`.
- Partitionnement envisageable si volumétrie élevée.
- Cache par tenant possible (Redis si activé ultérieurement).

Objectif : aucune dégradation notable en mode shared.

---

## 9. Sécurité & conformité

- Chiffrement au repos (RDS, S3).
- Séparation stricte des secrets via AWS Secrets Manager.
- Logs d’accès tenant-level.
- Audit trail des actions sur entités tenant.
- Procédure de suppression complète (hard delete contrôlé ou anonymisation selon règles légales).

---

## 10. Internationalisation

- Stockage langue par défaut dans `tenants`.
- Fallback automatique si traduction absente.
- Génération PDF et UI basées sur paramètre tenant.

---

## 11. Tests & validation

- Tests unitaires d’isolation.
- Tests d’intégration multi-tenant.
- Tests de non-régression sur changement de tenant.
- Vérification qu’aucune requête sans scope tenant n’est autorisée.