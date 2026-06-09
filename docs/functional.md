# DIAg – Spécification Fonctionnelle

## 1. Vision et objectifs
DIAg est une plateforme SaaS d’évaluation de la maturité en Intelligence Artificielle (IA) destinée aux entreprises et aux cabinets de conseil.

Objectifs :
- Évaluer le niveau de maturité IA d’une organisation via un référentiel structuré.
- Fournir un scoring global et détaillé par dimension.
- Générer des recommandations personnalisées et une roadmap priorisée.
- Permettre un benchmark sectoriel anonymisé.
- Suivre l’évolution de la maturité dans le temps.

---

## 2. Utilisateurs cibles
- PME
- ETI
- Grands groupes
- Cabinets de conseil / consultants IA
- Directions générales
- Directions de la transformation digitale
- DSI / CTO / équipes IT
- Responsables Innovation et Data

---

## 3. Modèle d’utilisation

### 3.1 Modes d’évaluation
- Auto-évaluation autonome.
- Évaluation accompagnée par un consultant.
- Un consultant peut gérer plusieurs clients et plusieurs diagnostics.

### 3.2 Multi-organisation
- Multi-tenant natif.
- Gestion de groupes et filiales.
- Historique complet des évaluations.
- Comparaison temporelle des résultats.

---

## 4. Référentiel de maturité

### 4.1 Référentiel administrable
Administrable via un back-office par les administrateurs plateforme :
- Dimensions
- Sous-dimensions
- Questions
- Types de réponses
- Pondérations
- Niveaux de maturité
- Recommandations associées

### 4.2 Versioning
- Gestion de versions (ex : Référentiel 2026, 2027).
- Référentiels personnalisés possibles.
- Chaque évaluation est liée à sa version de référentiel.
- Garantit la cohérence historique.

### 4.3 Axes couverts
- Stratégie IA
- Gouvernance
- Données et qualité des données
- Infrastructure technologique
- Cas d’usage IA
- Culture et compétences
- Sécurité et gestion des risques
- Conformité et réglementation
- Industrialisation et MLOps
- Mesure de la valeur et ROI

---

## 5. Processus d’évaluation

### 5.1 Questionnaire
- Questionnaire structuré.
- Scoring automatique.
- Pondération des dimensions et des questions.
- Questions critiques à fort impact.
- Évaluation individuelle ou collaborative.

### 5.2 Consolidation collaborative
- Agrégation des réponses par question.
- Moyenne pondérée possible selon le rôle.
- Pondérations configurables par profil.

### 5.3 Workflow
1. Brouillon
2. En cours
3. Soumis
4. En validation
5. Validé
6. Clôturé

Seule une évaluation validée peut générer un rapport officiel.

---

## 6. Scoring et résultats

### 6.1 Calcul des scores
- Score par dimension.
- Score global basé sur agrégation pondérée.
- Logique explicable et traçable.

### 6.2 Livrables
- Score global de maturité IA.
- Scores détaillés par dimension.
- Radar chart.
- Benchmark sectoriel.
- Rapport PDF détaillé.
- Recommandations personnalisées.
- Roadmap priorisée.
- Comparaison avec évaluations précédentes.

---

## 7. Benchmark sectoriel

### 7.1 Source des données
- Basé exclusivement sur les données agrégées anonymisées de la plateforme.

### 7.2 Segmentation
Comparaison par :
- Secteur d’activité
- Taille d’entreprise
- Zone géographique
- Référentiel utilisé

### 7.3 Règles
- Minimum 30 entreprises par segment.
- Anonymisation obligatoire.
- Aucune donnée individuelle visible.

---

## 8. Gestion documentaire

### 8.1 Upload
- Formats : PDF, DOCX, XLSX, PPTX
- Taille maximale : 100 Mo par document

### 8.2 Usages
- Consultation ultérieure
- Historisation
- Analyse IA
- Réévaluation

### 8.3 Évolutions prévues
- Indexation documentaire
- Recherche sémantique
- Architecture RAG par tenant

---

## 9. Internationalisation
- V1 multilingue : Français, Anglais.
- Architecture prête pour : Arabe, Espagnol, Allemand.
- Rapports PDF générés dans la langue sélectionnée.

---

## 10. Rôles et permissions
- Super Administrateur plateforme
- Administrateur organisation
- Consultant
- Évaluateur
- Lecteur / Direction

Gestion granulaire des permissions.

---

## 11. IA et recommandations

### 11.1 Fonctions IA
- Génération de recommandations personnalisées.
- Analyse documentaire.

### 11.2 Mode IA désactivable
- Paramétrable par tenant.
- Aucun appel externe si désactivé.
- Recommandations générées uniquement via moteur de règles.

### 11.3 Traçabilité
Chaque exécution conserve :
- Provider utilisé
- Version du modèle
- Prompt système
- Prompt utilisateur
- Réponse générée
- Horodatage
- Coût estimé

---