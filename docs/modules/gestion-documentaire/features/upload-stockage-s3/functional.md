## Fonctionnalité : Upload & stockage S3

### 1. Objectif métier
Permettre aux utilisateurs autorisés d’importer des documents d’entreprise (jusqu’à 100 Mo) dans la plateforme DIAg, de manière sécurisée, et de les rattacher à une organisation et, si nécessaire, à une évaluation spécifique. Cette fonctionnalité constitue la base documentaire exploitable pour les diagnostics, la consultation interne et les traitements IA futurs.

---

### 2. Périmètre
Inclus :
- Upload de documents via l’interface web.
- Validation des formats et de la taille maximale (100 Mo).
- Saisie des métadonnées associées.
- Rattachement obligatoire à une organisation et optionnel à une évaluation.
- Stockage sécurisé sur S3.
- Enregistrement des métadonnées en base.

Exclus :
- Consultation et téléchargement (traités par une autre fonctionnalité).
- Extraction de texte et indexation (préparées mais non détaillées ici).
- Analyse IA du document (gérée par le module IA).

---

### 3. Règles de gestion

#### 3.1 Formats et taille
- Formats autorisés : PDF, DOCX, XLSX, PPTX.
- Taille maximale : 100 Mo par document.
- Tout fichier non conforme est refusé avec message explicite.

#### 3.2 Rattachement
- Chaque document est obligatoirement rattaché à :
  - un tenant,
  - une organisation.
- Le rattachement à une évaluation est optionnel.
- Un document ne peut appartenir qu’à un seul tenant.

#### 3.3 Métadonnées obligatoires
- Titre.
- Type de document.
- Fichier source.

Métadonnées facultatives :
- Description.
- Date du document.
- Dimension du référentiel associée.

#### 3.4 Permissions
- Administrateur organisation : peut uploader et rattacher à toute évaluation de son organisation.
- Consultant : peut uploader pour les organisations dont il a la charge.
- Évaluateur : peut uploader dans le cadre des évaluations auxquelles il participe.
- Lecteur : ne peut pas uploader.

Tout upload est soumis au contrôle d’accès basé sur le tenant et les droits associés.

#### 3.5 Sécurité & conformité
- Isolation stricte par tenant.
- Chiffrement au repos et en transit.
- Journalisation de l’action d’upload (utilisateur, date, organisation, évaluation liée).
- Possibilité de suppression ultérieure conformément au RGPD (gérée par fonctionnalité dédiée).

---

### 4. Parcours utilisateur
1. L’utilisateur accède à l’espace documentaire (organisation ou évaluation).
2. Il clique sur "Ajouter un document".
3. Il sélectionne un fichier et renseigne les métadonnées.
4. Le système valide les règles (format, taille, permissions).
5. Le document est stocké de manière sécurisée.
6. Une confirmation est affichée.

Gestion des erreurs :
- Format invalide.
- Taille dépassée.
- Droits insuffisants.
- Échec technique d’upload.

---

### 5. Critères d’acceptation
- ✅ Un utilisateur autorisé peut uploader un fichier valide < 100 Mo.
- ✅ Le document est visible dans la liste après upload.
- ✅ Le document est correctement rattaché à l’organisation et, le cas échéant, à l’évaluation.
- ✅ Un utilisateur non autorisé ne peut pas uploader.
- ✅ Un fichier > 100 Mo est refusé.
- ✅ Un format non autorisé est refusé.
- ✅ L’action d’upload est tracée dans l’audit trail.
- ✅ Le document n’est accessible qu’au tenant concerné.

---

### 6. Contraintes non fonctionnelles
- Robustesse de l’upload (gestion des erreurs réseau).
- Performance compatible avec SLA global.
- Cohérence multi-tenant quel que soit le mode d’isolation (shared, DB dédiée, infra dédiée).