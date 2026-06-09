## Spécification technique – Upload & stockage S3

### 1. Composants impactés
- Frontend React (formulaire d’upload, gestion erreurs).
- API Laravel (endpoint upload, validation, policies).
- PostgreSQL (table documents).
- Amazon S3 (stockage fichier).
- Middleware Auth & Tenant resolution.
- Audit logging.

---

### 2. API Backend

#### 2.1 Endpoint
`POST /api/documents`

Payload (multipart/form-data) :
- file (binaire)
- title (string)
- description (string, nullable)
- document_type (string)
- organisation_id (uuid)
- evaluation_id (uuid, nullable)
- document_date (date, nullable)
- dimension_id (uuid, nullable)

#### 2.2 Validations
- Vérification présence tenant courant.
- Vérification appartenance organisation au tenant.
- Vérification droits via Laravel Policies.
- Validation MIME type.
- Vérification taille ≤ 100 Mo.

Retour :
- 201 Created + métadonnées document.
- 400/403/422 selon cas d’erreur.

---

### 3. Stockage S3

#### 3.1 Convention de nommage
Clé S3 :  
`tenant/{tenant_id}/org/{organisation_id}/documents/{uuid}`

- UUID généré côté backend.
- Aucun nom original utilisé comme clé principale.

#### 3.2 Sécurité
- Bucket privé.
- Accès via IAM role ECS.
- Chiffrement SSE activé.
- Accès lecture via URL pré-signée (hors périmètre de cette fonctionnalité mais compatible).

---

### 4. Persistance en base
Insertion dans table `documents` :
- id (uuid)
- tenant_id
- organisation_id
- evaluation_id
- title
- description
- document_type
- file_path (clé S3)
- file_name_original
- file_size
- mime_type
- version_number (1 par défaut)
- status = active
- created_by
- timestamps

Transaction :
- Upload S3 + insertion DB doivent être cohérents.
- En cas d’échec DB après upload S3 → suppression du fichier pour éviter les orphelins.

---

### 5. Multi-tenancy
- tenant_id injecté automatiquement via contexte.
- Aucune requête sans filtre tenant.
- Compatible avec :
  - Base partagée (tenant_id obligatoire en index).
  - Base dédiée (logique identique sans impact applicatif).
  - Infrastructure dédiée.

---

### 6. Journalisation & audit
À chaque upload :
- user_id
- tenant_id
- organisation_id
- evaluation_id
- document_id
- timestamp
- type_action = DOCUMENT_UPLOADED

Logs applicatifs envoyés vers CloudWatch.

---

### 7. Extensions prévues
- Déclenchement d’un job asynchrone (SQS) pour :
  - extraction texte,
  - prétraitement IA,
  - indexation future.

Cette émission d’événement doit être découplée pour ne pas bloquer la réponse API.

---

### 8. Contraintes techniques
- Timeout API compatible avec upload 100 Mo.
- Protection contre upload malveillant (validation MIME stricte).
- Structure prête pour antivirus asynchrone futur.
- Respect RGPD (suppression définitive possible ultérieurement).

---

### 9. Dépendances
Dépend :
- Authentification & gestion des rôles.
- Gestion organisations.
- Infrastructure S3.

Impacte :
- Consultation documentaire.
- Analyse IA.
- Indexation / RAG futur.