# Module : Gestion documentaire

## 1. Rôle du module dans DIAg
Le module **Gestion documentaire** permet aux organisations d’importer, stocker, consulter et historiser des documents liés à leur évaluation de maturité IA. Il constitue la base documentaire exploitée lors des diagnostics et prépare l’architecture à des usages avancés tels que l’analyse IA, l’indexation sémantique et le RAG (Retrieval Augmented Generation) par tenant.

Il s’intègre aux modules :
- Évaluations (documents associés à une évaluation)
- IA (analyse documentaire, génération de recommandations)
- Administration (paramétrage et contrôle)
- Sécurité & Audit (traçabilité complète)

---

## 2. Fonctionnalités principales

### 2.1 Upload de documents
- Upload via interface web.
- Formats autorisés : PDF, DOCX, XLSX, PPTX.
- Taille maximale : 100 Mo par document.
- Association à :
  - une organisation
  - éventuellement une évaluation spécifique
  - une dimension du référentiel (optionnel)
- Ajout de métadonnées :
  - Titre
  - Description
  - Type de document (ex : stratégie, gouvernance, sécurité…)
  - Date du document

Règles de gestion :
- Vérification du format et de la taille côté frontend et backend.
- Refus des fichiers non conformes.
- Un document appartient toujours à un tenant.

---

### 2.2 Stockage sécurisé
- Stockage centralisé et sécurisé.
- Isolation stricte par tenant.
- Chiffrement des données au repos et en transit.

Règles de gestion :
- Aucun document d’un tenant ne doit être accessible à un autre.
- Suppression logique (soft delete) avec possibilité de suppression définitive selon règles RGPD.

---

### 2.3 Consultation et téléchargement
- Liste des documents par organisation.
- Filtres : type, date, évaluation liée.
- Téléchargement sécurisé.
- Visualisation basique (selon format supporté par le navigateur).

Permissions :
- Administrateur organisation : gestion complète.
- Consultant : accès selon périmètre client.
- Évaluateur : upload et consultation.
- Lecteur : consultation uniquement.

---

### 2.4 Historisation
- Conservation des versions si un document est remplacé.
- Historique des actions : upload, modification métadonnées, suppression.
- Traçabilité complète (audit trail).

---

### 2.5 Support de l’analyse IA
Le module doit permettre :
- L’envoi contrôlé de documents vers la couche IA.
- L’anonymisation ou le filtrage des données personnelles avant traitement externe.
- La désactivation de toute analyse IA si le tenant a désactivé l’IA.

---

### 2.6 Préparation à l’indexation et RAG par tenant
Prévoir une architecture compatible avec :
- Extraction de texte.
- Indexation sémantique.
- Recherche ultérieure.
- Base documentaire isolée par tenant pour usage RAG.

Règles :
- Aucune mutualisation des embeddings entre tenants.
- Respect des règles RGPD.

---

## 3. Contraintes fonctionnelles
- Multi-tenant natif.
- Respect strict des permissions.
- Conformité RGPD (droit à l’oubli, suppression définitive).
- Compatibilité multi-langue (métadonnées traduisibles si nécessaire).
- Performance compatible avec SLA global (upload fiable, consultation < 2s pour listing standard).