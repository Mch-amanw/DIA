## 1. Objectif métier
Mettre en place une couche d’abstraction LLM multi-providers permettant à la plateforme DIAg d’utiliser différents fournisseurs de modèles d’IA (Azure OpenAI, Mistral, Anthropic, OpenAI) sans dépendance directe au code métier.

Cette abstraction garantit :
- La continuité de service en cas d’indisponibilité d’un provider.
- La flexibilité contractuelle et financière.
- La conformité aux exigences de sécurité et de traçabilité.
- L’indépendance technologique de la plateforme.

---

## 2. Périmètre fonctionnel

### 2.1 Providers supportés
- Azure OpenAI (provider principal)
- Mistral
- Anthropic
- OpenAI

La fonctionnalité doit permettre d’utiliser ces providers pour :
- Génération de texte (recommandations, synthèses, analyses).
- Analyse documentaire.

---

### 2.2 Sélection du provider
Le système doit permettre :
- Une configuration globale par défaut (provider principal).
- Une configuration par tenant (override possible).
- Une désactivation complète des appels IA au niveau du tenant.

En cas de désactivation IA :
- Aucun appel externe ne doit être effectué.
- Les fonctionnalités dépendantes doivent basculer vers un moteur de règles interne.

---

### 2.3 Neutralité fonctionnelle
Les modules métiers (Recommandations, Analyse documentaire, Reporting enrichi IA) ne doivent :
- Connaître ni le provider utilisé.
- Ni dépendre d’un format propriétaire spécifique.

L’abstraction fournit une interface unifiée de génération.

---

### 2.4 Traçabilité obligatoire
Chaque appel IA doit enregistrer :
- Provider utilisé
- Modèle
- Prompt système
- Prompt utilisateur
- Réponse générée
- Horodatage
- Coût estimé
- Tenant concerné

Ces informations doivent être consultables dans l’audit trail.

---

### 2.5 Gestion des erreurs et résilience
Le système doit :
- Gérer les erreurs provider (timeout, quota, indisponibilité).
- Journaliser les échecs.
- Permettre une stratégie de fallback vers un provider secondaire si configuré.

Aucune erreur brute du provider ne doit être exposée à l’utilisateur final.

---

## 3. Règles de gestion
- Tout appel IA passe obligatoirement par la couche d’abstraction.
- Aucun appel direct à un SDK provider n’est autorisé dans le code métier.
- Les clés API sont stockées dans AWS Secrets Manager.
- Les données envoyées aux LLM doivent respecter les règles RGPD (anonymisation si nécessaire).
- Si le seuil de segment benchmark (30 entreprises) n’est pas respecté, aucune donnée agrégée ne peut être injectée dans un prompt.

---

## 4. Critères d’acceptation
- ✅ Un changement de provider par configuration ne nécessite aucune modification du code métier.
- ✅ Les appels sont correctement journalisés avec tous les champs requis.
- ✅ En cas d’indisponibilité du provider principal, le fallback fonctionne si configuré.
- ✅ En mode IA désactivé, aucun appel externe n’est effectué.
- ✅ Les réponses sont cohérentes quel que soit le provider utilisé.
- ✅ Les logs permettent d’auditer précisément un résultat généré.

---

## 5. Hors périmètre
- Conception des prompts métier.
- Logique de scoring.
- UI d’administration détaillée (couverte par module Administration).