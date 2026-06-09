# 1. Présentation du projet

## 1.1 Objectif
Développer une plateforme SaaS d’évaluation de la maturité en Intelligence Artificielle (IA) destinée aux entreprises et aux cabinets de conseil.

L’outil permet :
- D’évaluer le niveau de maturité IA d’une organisation.
- D’identifier les points forts et axes d’amélioration.
- De générer automatiquement des recommandations explicables.
- De produire une feuille de route priorisée et personnalisée.
- De comparer les résultats à des benchmarks sectoriels.

L’outil doit être perçu comme un diagnostic neutre et crédible, tout en constituant un levier d’accélération commerciale pour les consultants.

---

# 2. Cibles et utilisateurs

## 2.1 Organisations cibles
- PME
- ETI
- Grands groupes
- Tous secteurs d’activité

## 2.2 Profils utilisateurs
- **Super Administrateur (plateforme)**
- **Administrateur entreprise**
- **Consultant (multi-entreprises)**
- **Évaluateur**
- **Lecteur / Direction**

La plateforme est multi-entreprises et permet à un consultant de gérer plusieurs clients.

---

# 3. Modèle de maturité IA

## 3.1 Dimensions d’évaluation
Le référentiel intégré comprend les dimensions suivantes :
- Stratégie et vision IA
- Gouvernance et conformité
- Données et qualité des données
- Infrastructure et technologies
- Cas d’usage et automatisation
- Compétences et culture IA
- Sécurité et gestion des risques
- Pilotage et mesure de la valeur

## 3.2 Niveaux de maturité
Chaque dimension est évaluée sur 5 niveaux :
1. Initial
2. Émergent
3. Structuré
4. Avancé
5. Optimisé

## 3.3 Pondérations
- Le score global repose sur une pondération des dimensions.
- Une pondération par défaut est définie dans le référentiel.
- Les consultants peuvent personnaliser les pondérations.
- Certaines questions sont critiques et ont un poids supérieur.
- Les dimensions « Gouvernance », « Données » et « Cas d’usage » ont un impact plus important sur le score final.

---

# 4. Format d’évaluation

## 4.1 Questionnaire structuré
Types de questions :
- Échelle de maturité (1 à 5)
- Choix multiples
- Oui / Non
- Réponses textuelles facultatives

## 4.2 Version avancée
- Import de documents
- Analyse documentaire assistée par IA
- Interviews guidées

---

# 5. Parcours d’évaluation

## 5.1 Modes disponibles
### Mode individuel
Une seule personne complète l’évaluation.

### Mode collaboratif
Participants multiples par dimension (Direction, DSI, RH, Métiers, Innovation).

Workflow :
1. Création de l’évaluation
2. Invitation des participants
3. Réponses individuelles
4. Consolidation
5. Validation finale
6. Génération du rapport

La génération du rapport définitif nécessite validation par le responsable.

---

# 6. Scoring et logique métier

## 6.1 Scoring
- Moteur déterministe basé sur le référentiel.
- Pondération des dimensions.
- Pondération spécifique des questions critiques.
- Score global + score par dimension.

## 6.2 Explicabilité
Chaque recommandation doit afficher :
- La dimension concernée
- Les réponses ayant conduit à la recommandation
- Le niveau constaté
- L’objectif cible
- La justification

---

# 7. Recommandations et IA

## 7.1 Architecture hybride
- Moteur de règles → scoring et recommandations de base
- LLM → enrichissement contextuel métier et sectoriel

Le moteur déterministe reste prioritaire pour garantir cohérence et auditabilité.

## 7.2 Fonctionnalités IA
- Analyse intelligente des réponses
- Génération de recommandations personnalisées
- Génération de plans d’action
- Analyse documentaire
- Résumé exécutif automatique
- Détection d’écarts de maturité
- Suggestions de cas d’usage IA

Mode IA désactivable pour clients sensibles.

---

# 8. Roadmap priorisée

## 8.1 Critères de priorisation
- Impact métier estimé
- Effort de mise en œuvre
- Niveau de maturité actuel
- Dépendances
- Potentiel de création de valeur
- Identification des quick wins

Chaque action reçoit :
- Score d’impact
- Score d’effort
- Niveau de priorité

## 8.2 Paramètres ajustables
- Budget disponible
- Capacité interne
- Horizon temporel (3, 6, 12, 24 mois)

La roadmap est recalculée dynamiquement.

---

# 9. Restitution des résultats

À la fin de l’évaluation :
- Score global
- Score par dimension
- Radar chart
- Analyse des écarts
- Recommandations priorisées
- Roadmap
- Rapport PDF téléchargeable

---

# 10. Benchmark

## 10.1 Comparaisons disponibles
- Par secteur
- Par taille d’entreprise
- Par région / pays

## 10.2 Conditions
- Minimum 30 entreprises par segment
- Données agrégées et anonymisées
- Aucune réidentification possible
- Conformité RGPD

---

# 11. Référentiel administrable

Back-office permettant :
- Gestion des dimensions
- Sous-dimensions
- Questions
- Pondérations
- Niveaux
- Recommandations

## 11.1 Versioning
- Référentiel versionné (ex : 2026, 2027)
- Versions personnalisées possibles
- Les évaluations restent rattachées à leur version d’origine

---

# 12. Modèle économique

SaaS par abonnement :
- Version gratuite (diagnostic simplifié)
- Version Premium (rapport détaillé + recommandations)
- Offre Consultant (multi-clients)
- Offre Entreprise (multi-utilisateurs)

---

# 13. Indicateurs de performance (KPIs)

## Adoption
- Nombre d’entreprises inscrites
- Nombre d’évaluations lancées
- Nombre d’évaluations terminées

## Engagement
- Temps moyen de complétion
- Taux de complétion
- Nombre de réévaluations

## Business
- Conversion gratuit → premium
- MRR
- Nombre de consultants actifs
- Nombre d’entreprises par consultant

## Valeur délivrée
- Nombre de roadmaps générées
- Recommandations appliquées
- Évolution du score dans le temps