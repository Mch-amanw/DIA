## Ticket : Upload sécurisé vers S3 – Spécification technique

### 1. Endpoint API

**Route :** `POST /api/documents`

**Content-Type :** `multipart/form-data`

#### Payload attendu
- file (binary, required)
- title (string, required)
- description (string, nullable)
- document_type (string, required)
- organisation_id (uuid, required)
- evaluation_id (uuid, nullable)
- document_date (date, nullable)
- dimension_id (uuid, nullable)

---

### 2. Validation Backend

#### 2.1 Validation métier
- Résolution du `tenant_id` depuis le contexte authentifié.
- Vérification que `organisation_id` appartient au tenant.
- Si `evaluation_id` fourni :
  - Vérifier qu’elle appartient à la même organisation.
- Vérification des permissions via Laravel Policies.

#### 2.2 Validation fichier
- Validation MIME type stricte (whitelist).
- Vérification taille ≤ 100 Mo.
- Vérification présence du fichier.

Erreurs retournées :
- 400 : requête invalide.
- 403 : accès interdit.
- 422 : validation échouée.

---

### 3. Génération identifiant et stockage S3

#### 3.1 Génération identifiant
- Génération d’un UUID v4 côté backend pour `documents.id`.

#### 3.2 Clé S3
Convention :
`tenant/{tenant_id}/org/{organisation_id}/documents/{uuid}`

- Aucun nom original dans la clé.
- Bucket privé.
- Chiffrement SSE activé.

#### 3.3 Upload
- Upload via SDK AWS depuis l’API.
- Rôle IAM ECS avec permissions restreintes au bucket.

---

### 4. Persistance base de données

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
- version_number = 1
- status = active
- created_by
- created_at / updated_at

#### Cohérence transactionnelle
- Étape 1 : Upload S3.
- Étape 2 : Transaction DB.
- Si insertion DB échoue → suppression immédiate du fichier S3.
- Si upload S3 échoue → aucune insertion DB.

---

### 5. Audit & Logs

Création d’un enregistrement d’audit :
- user_id
- tenant_id
- organisation_id
- evaluation_id
- document_id
- action = DOCUMENT_UPLOADED
- timestamp

Logs applicatifs envoyés vers CloudWatch.

---

### 6. Multi-tenancy
- `tenant_id` obligatoire dans toutes les requêtes DB.
- Index recommandé sur `(tenant_id)`.
- Compatible avec :
  - base partagée (filtrage logique),
  - base dédiée (même logique applicative),
  - infrastructure dédiée.

Aucune requête ne doit être exécutée sans contrainte explicite sur le tenant.

---

### 7. Extensions prévues (non implémentées ici)
- Dispatch d’un event `DocumentUploaded`.
- Envoi asynchrone vers SQS pour extraction texte.
- Intégration future antivirus.

Le code doit être structuré pour permettre l’ajout ultérieur de ces traitements sans refactor majeur.

---

### 8. Sécurité
- HTTPS obligatoire.
- Validation stricte des types MIME.
- Pas d’exposition publique du bucket.
- Accès uniquement via IAM role.
- Respect RGPD : suppression ultérieure possible via fonctionnalité dédiée.

---

### 9. Dépendances
- Module Authentification.
- Module Gestion organisations.
- Infrastructure AWS (S3, IAM, ECS).
- Middleware tenant resolution.

Ce ticket constitue la brique fondamentale du stockage documentaire sécurisé dans DIAg.