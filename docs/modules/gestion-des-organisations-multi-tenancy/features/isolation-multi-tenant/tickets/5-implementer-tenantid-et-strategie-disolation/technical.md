## Implémentation technique – `tenant_id` et stratégie d’isolation

### 1. Ajout du champ `tenant_id`

#### 1.1 Tables concernées

Toutes les tables métier doivent inclure :

- `tenant_id` (UUID, non nullable)
- Index sur `tenant_id`

Exceptions :
- Tables strictement globales (ex : `tenants`, configuration plateforme globale).

#### 1.2 Contraintes

- Foreign key vers `tenants.id` en mode Shared.
- Index simple ou composite (`tenant_id`, `created_at`) selon volumétrie.
- Interdiction de table métier sans `tenant_id`.

---

### 2. Global Scope Eloquent

#### 2.1 Création d’un TenantScope

- Implémenter un Global Scope Laravel appliqué automatiquement.
- Le scope doit :
  - Ajouter `where tenant_id = currentTenantId()`.
  - Être appliqué à tous les modèles concernés.

#### 2.2 Trait TenantAware

Créer un trait réutilisable :

- Ajout automatique du scope.
- Remplissage automatique du `tenant_id` lors du `creating`.
- Blocage si aucun tenant actif n’est défini.

Tous les modèles métier doivent utiliser ce trait.

---

### 3. Résolution du tenant courant

#### 3.1 Middleware

Créer un middleware global :

Responsabilités :
- Extraire le `tenant_id` depuis :
  - JWT / session utilisateur.
- Vérifier que l’utilisateur appartient au tenant.
- Injecter le tenant courant dans :
  - Service container
  - Context applicatif (ex : singleton TenantContext)
- Refuser la requête si incohérence.

---

### 4. Abstraction de la connexion base de données

#### 4.1 Préparation multi-connexion

Mettre en place une couche de résolution de connexion :

- Service `TenantConnectionResolver`.
- En mode Shared : retourne connexion par défaut.
- Prévoir structure permettant ultérieurement :
  - Sélection dynamique d’une connexion dédiée.

Aucune logique métier ne doit dépendre du type de connexion.

---

### 5. Sécurisation des requêtes

- Interdire l’utilisation de requêtes brutes non scoppées.
- Ajouter un mécanisme de détection en environnement de test :
  - Vérification qu’un modèle métier sans scope déclenche une erreur.
- Revue des migrations pour imposer `tenant_id`.

---

### 6. Préparation pour traitements asynchrones

- Définir une interface ou trait pour Jobs Laravel :
  - Stockage explicite du `tenant_id`.
  - Méthode d’initialisation du contexte tenant à l’exécution.

L’implémentation complète des jobs multi-tenant pourra être finalisée dans un ticket dédié.

---

### 7. Journalisation & audit

- Ajouter `tenant_id` dans les logs structurés.
- Vérifier que toute entrée d’audit inclut le tenant.

---

### 8. Tests à prévoir dans ce ticket

- Test unitaire du Global Scope.
- Test d’injection automatique du `tenant_id`.
- Test middleware : blocage si accès cross-tenant.
- Test résolution connexion (mode Shared).

---

### 9. Contraintes non fonctionnelles

- Aucune dégradation notable des performances.
- Ajout d’index obligatoire.
- Compatible PostgreSQL.
- Respect architecture modulaire Laravel.

---

### 10. Dépendances

Dépend de :
- Module Authentification (JWT contenant `tenant_id`).
- Table `tenants` existante.

Conditionne :
- Tous les modules métier.
- Implémentation Dedicated Database.
- Implémentation Dedicated Infrastructure.

Ce ticket est critique et doit être validé avant toute montée en charge fonctionnelle.