## Ticket : Upload sécurisé vers S3

### 1. Objectif
Implémenter le mécanisme complet d’upload sécurisé des documents vers Amazon S3, incluant :
- la validation des formats autorisés,
- la vérification de la taille maximale (100 Mo),
- le contrôle des permissions,
- le stockage sécurisé du fichier,
- l’enregistrement des métadonnées en base.

Ce ticket couvre la chaîne serveur garantissant que tout document importé respecte les règles métier, de sécurité et de multi-tenancy définies dans le module Gestion documentaire.

---

### 2. Périmètre fonctionnel
Inclus dans ce ticket :
- Réception d’un fichier via API (multipart/form-data).
- Validation des champs obligatoires.
- Vérification des formats autorisés (PDF, DOCX, XLSX, PPTX).
- Vérification de la taille maximale (≤ 100 Mo).
- Vérification des droits utilisateur (selon rôle et périmètre organisation/évaluation).
- Stockage du fichier dans S3 avec isolation stricte par tenant.
- Enregistrement des métadonnées dans la base PostgreSQL.
- Journalisation de l’action d’upload.

Exclus :
- Consultation et téléchargement.
- Extraction de texte et indexation.
- Analyse IA du document.
- Gestion avancée des versions.

---

### 3. Règles de gestion appliquées

#### 3.1 Validation fichier
- Seuls les formats suivants sont acceptés : PDF, DOCX, XLSX, PPTX.
- Taille maximale autorisée : 100 Mo.
- Tout fichier non conforme est rejeté avec message explicite.

#### 3.2 Rattachement obligatoire
- Le document doit être rattaché à :
  - un tenant (déduit du contexte authentifié),
  - une organisation appartenant à ce tenant.
- Le rattachement à une évaluation est optionnel.
- Si une évaluation est fournie, elle doit appartenir à la même organisation.

#### 3.3 Permissions
Le système vérifie que l’utilisateur :
- appartient au tenant concerné,
- dispose des droits d’upload pour l’organisation et/ou l’évaluation ciblée.

Aucun upload n’est autorisé sans validation explicite des permissions.

#### 3.4 Sécurité
- Isolation stricte des documents par tenant.
- Le nom original du fichier ne doit pas être utilisé comme clé principale de stockage.
- Toutes les opérations sont tracées dans l’audit trail.

---

### 4. Flux fonctionnel
1. L’utilisateur soumet le formulaire d’upload.
2. L’API valide les données (format, taille, rattachement, permissions).
3. Le système génère un identifiant unique pour le document.
4. Le fichier est stocké sur S3 dans un espace isolé par tenant.
5. Les métadonnées sont enregistrées en base.
6. L’action est journalisée.
7. La réponse API retourne les métadonnées du document créé.

En cas d’erreur à une étape, aucune donnée incohérente (fichier orphelin ou entrée base isolée) ne doit subsister.

---

### 5. Contraintes non fonctionnelles
- Cohérence transactionnelle entre S3 et base de données.
- Respect du modèle multi-tenant (shared, DB dédiée, infra dédiée).
- Robustesse face aux erreurs réseau.
- Temps de réponse compatible avec upload de 100 Mo.
- Traçabilité complète pour audit et conformité RGPD.