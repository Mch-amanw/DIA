# Module : Gestion documentaire – Spécification technique

## 1. Architecture générale

### 1.1 Intégration applicative
Module intégré au monolithe Laravel (API REST).
Frontend : React + TypeScript.

Responsabilités :
- Gestion des métadonnées en base PostgreSQL.
- Gestion des fichiers via Amazon S3.
- Déclenchement éventuel de traitements asynchrones via SQS.

---

## 2. Modèle de données (logique)

### 2.1 Entité Document
Champs principaux :
- id
- tenant_id
- organisation_id
- evaluation_id (nullable)
- title
- description
- document_type
- file_path (S3 key)
- file_name_original
- file_size
- mime_type
- version_number
- status (active, deleted)
- created_by
- created_at
- updated_at
- deleted_at (soft delete)

Index recommandés :
- (tenant_id)
- (organisation_id)
- (evaluation_id)
- (tenant_id, document_type)

Isolation assurée par tenant_id.

---

## 3. Stockage fichier

### 3.1 Amazon S3
- Bucket dédié ou préfixe structuré par tenant.
- Convention de nommage :
  `tenant/{tenant_id}/org/{organisation_id}/documents/{uuid}`
- Chiffrement SSE activé.
- Accès privé uniquement via URLs signées.

### 3.2 Téléchargement
- Génération d’URL pré-signée temporaire.
- Durée de validité courte.

---

## 4. Upload & validation

### 4.1 Backend
- Validation MIME type.
- Vérification taille max (100 Mo).
- Scan antivirus possible via service externe (optionnel évolutif).

### 4.2 Traitements asynchrones
Via SQS + Laravel Queues pour :
- Extraction de texte.
- Prétraitement IA.
- Indexation future.

---

## 5. Sécurité

- Isolation stricte par tenant_id.
- Contrôle d’accès via middleware Laravel + policies.
- Journalisation complète des actions (création, consultation, suppression).
- Chiffrement en transit (HTTPS).
- Suppression définitive : suppression S3 + purge base.

Conformité RGPD :
- Endpoint de suppression totale.
- Traçabilité de la suppression.

---

## 6. Intégration avec module IA

- API interne pour transmettre un document à analyser.
- Passage par couche d’abstraction LLM.
- Journalisation obligatoire : provider, modèle, prompts, réponse, coût.
- Respect du flag "IA désactivée" au niveau tenant.

---

## 7. Préparation RAG

### 7.1 Extraction texte
- Pipeline asynchrone.
- Stockage du texte extrait en base ou stockage dédié.

### 7.2 Embeddings (évolution)
- Stockage isolé par tenant.
- Aucune indexation cross-tenant.
- Possibilité d’intégration future avec OpenSearch ou base vectorielle.

---

## 8. Performance & SLA

- Upload robuste avec gestion d’erreurs.
- Listing paginé.
- Requêtes indexées par tenant.
- Objectif :
  - Listing < 2 secondes.
  - Téléchargement immédiat via URL signée.

---

## 9. Monitoring & logs

- Logs applicatifs dans CloudWatch.
- Traçabilité des erreurs upload.
- Monitoring volumétrie stockage par tenant.

---

## 10. Dépendances

Dépend :
- Module Authentification
- Module Gestion organisations
- Module IA
- Infrastructure AWS (S3, SQS, RDS)

Impacte :
- Reporting (usage documentaire)
- Benchmark (indirect via analyses IA futures)