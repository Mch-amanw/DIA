# 1. Architecture générale

## 1.1 Type d’application
Application SaaS accessible via navigateur.

## 1.2 Stack technique
- Frontend : React + TypeScript
- Backend : Laravel (API REST)
- Base de données : PostgreSQL
- Hébergement : AWS
- Stockage fichiers : Amazon S3

Architecture orientée API avec séparation claire Frontend / Backend.

---

# 2. Architecture fonctionnelle

## 2.1 Modules principaux
- Gestion des organisations (multi-entreprises)
- Gestion des utilisateurs et rôles
- Moteur d’évaluation
- Moteur de scoring déterministe
- Moteur de recommandations (règles)
- Module IA (LLM)
- Générateur de roadmap dynamique
- Module benchmark
- Génération de rapports PDF
- Back-office d’administration du référentiel
- Module de versioning

---

# 3. Moteur de scoring

## 3.1 Caractéristiques
- Déterministe
- Pondération configurable
- Support des questions critiques
- Pondérations personnalisables par consultant
- Support du versioning du référentiel

## 3.2 Traçabilité
- Stockage des réponses brutes
- Stockage des pondérations appliquées
- Conservation de la version du référentiel
- Journalisation des calculs

---

# 4. Architecture IA

## 4.1 Approche hybride
1. Moteur de règles interne (Laravel)
2. Appels à un LLM externe pour enrichissement

Le scoring et les conclusions principales ne dépendent pas du LLM.

## 4.2 Exigences IA
- Mode IA activable / désactivable par client
- Aucune utilisation des données pour entraîner des modèles tiers
- Préférence pour traitement dans l’UE
- Journalisation complète des appels IA

## 4.3 Auditabilité
- Conservation des prompts
- Conservation des réponses générées
- Traçabilité des recommandations enrichies

---

# 5. Roadmap dynamique

- Algorithme de priorisation basé sur impact, effort, maturité, dépendances
- Recalcul dynamique selon budget, capacité et horizon temporel
- Stockage des paramètres utilisés pour traçabilité

---

# 6. Benchmark

## 6.1 Calcul
- Agrégation statistique par segment
- Seuil minimal : 30 entreprises

## 6.2 Sécurité
- Données anonymisées
- Aucune exposition de données individuelles
- Conformité RGPD

---

# 7. Sécurité et conformité

- Authentification sécurisée
- Gestion des rôles et permissions
- Journalisation des actions utilisateurs
- Sauvegardes automatiques
- Isolation logique des données multi-entreprises
- Conformité RGPD

---

# 8. Gestion documentaire

- Upload vers S3
- Stockage sécurisé
- Analyse documentaire via module IA (si activé)

---

# 9. Versioning

- Versionnement du référentiel
- Association stricte évaluation ↔ version
- Maintien des historiques

---

# 10. Scalabilité

- Hébergement AWS
- Architecture compatible montée en charge SaaS
- Séparation stockage applicatif / stockage fichiers