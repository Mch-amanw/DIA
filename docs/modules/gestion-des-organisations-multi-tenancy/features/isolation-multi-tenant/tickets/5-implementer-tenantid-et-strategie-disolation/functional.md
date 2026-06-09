## Ticket : Implémenter `tenant_id` et stratégie d’isolation

### 1. Objectif
Mettre en place le socle technique garantissant l’isolation logique des données entre tenants en mode **Shared**, tout en préparant l’architecture pour supporter ultérieurement les modes **Dedicated Database** et **Dedicated Infrastructure**, sans modification des modules métier.

Ce ticket constitue une brique critique de la fonctionnalité *Isolation multi-tenant* et conditionne la sécurité, la conformité RGPD et la fiabilité globale de la plateforme.

---

### 2. Périmètre fonctionnel

Ce ticket couvre :

1. L’introduction systématique du champ `tenant_id` dans les tables métier.
2. L’application automatique du filtrage des données par tenant.
3. La résolution du tenant courant dans chaque requête.
4. La propagation du contexte tenant dans les traitements synchrones.
5. La préparation de l’architecture pour supporter ultérieurement des bases dédiées.

Hors périmètre :
- Provisioning complet des bases dédiées.
- Déploiement d’infrastructure AWS dédiée.
- Suppression complète d’un tenant.

---

### 3. Règles fonctionnelles implémentées

1. Toute donnée métier doit être rattachée à un unique tenant.
2. Aucune requête applicative ne doit retourner de données appartenant à un autre tenant.
3. Le tenant actif doit être explicitement déterminé pour chaque requête.
4. Les modules métier (Évaluations, Référentiel, Reporting, IA, Benchmark, etc.) ne doivent contenir aucune logique spécifique au mode d’isolation.
5. L’architecture doit permettre d’introduire une base dédiée par tenant sans refactorisation métier.

---

### 4. Impacts sur les autres modules

- Tous les modules métier devront utiliser le mécanisme central d’isolation.
- Les futures migrations devront intégrer `tenant_id` par défaut.
- Les traitements asynchrones devront inclure le contexte tenant (préparation technique dans ce ticket).

---

### 5. Risques couverts

- Fuite de données inter-tenant.
- Requêtes non scoppées.
- Incohérence de contexte utilisateur.
- Impossibilité d’évoluer vers des bases dédiées.

Ce ticket est bloquant pour toute implémentation métier ultérieure.