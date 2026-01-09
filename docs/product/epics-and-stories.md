# Epics & User Stories - KeepStreak

Ce document traduit les décisions architecturales et les besoins fonctionnels en unités de travail concrètes (Epics et User Stories) pour guider le développement.

---

## Epic 1 : Baseline & Infrastructure 🏗️
*Mise en place de l'environnement de développement et des bases du monorepo.*

### User Stories :
- **US 1.1** : En tant que dév, je veux un monorepo Turborepo fonctionnel avec NestJS 11 et Next.js 16.
- **US 1.2** : En tant que dév, je veux un schéma Prisma partagé dans `packages/db` connecté à Neon PostgreSQL.
- **US 1.3** : En tant que dév, je veux un package `packages/types` pour partager les DTOs entre l'API et le Web.
- **US 1.4** : En tant que dév, je veux une configuration ESLint/Prettier unifiée pour garantir la qualité du code.

---

## Epic 2 : Core API - Sécurité & Auth 🛡️
*Implémentation des protocoles de sécurité et de l'authentification.*

### User Stories :
- **US 2.1** : En tant qu'utilisateur, je veux me connecter via GitHub OAuth 2.0 pour accéder à mon dashboard.
- **US 2.2** : En tant que système, je veux chiffrer les données sensibles (AES-256-GCM) avant de les stocker en base.
- **US 2.3** : En tant que système, je veux générer des signatures HMAC SHA-256 pour valider les requêtes des workers.
- **US 2.4** : En tant qu'utilisateur, je veux que mes données soient isolées de celles des autres (Multi-tenancy logic).

---

## Epic 3 : Stealth Orchestration Engine 📡
*Cerveau de planification et système de messagerie distribuée.*

### User Stories :
- **US 3.1** : En tant que dév, je veux intégrer Redis Streams pour gérer la file d'attente des commandes de pulse.
- **US 3.2** : En tant que système, je veux un scheduler qui calcule les prochaines exécutions avec un Jitter (bruit aléatoire).
- **US 3.3** : En tant que système, je veux gérer les retries et les acquittements (ACK) des tâches via les Consumer Groups Redis.
- **US 3.4** : En tant que système, je veux générer des messages de commit aléatoires basés sur des templates prédéfinis.

---

## Epic 4 : Shadow Worker Implementation 🤖
*Développement de l'agent distant (GitHub Action).*

### User Stories :
- **US 4.1** : En tant qu'agent, je veux un script de worker capable de faire un "check-in" vers l'API avec un handshake HMAC.
- **US 4.2** : En tant qu'agent, je veux exécuter des commits discrets sur le repository `keepstreak-pulse` de l'utilisateur.
- **US 4.3** : En tant qu'agent, je veux pouvoir me mettre à jour automatiquement si une nouvelle version du script est disponible sur le Cerveau.
- **US 4.4** : En tant qu'utilisateur, je veux que le repo du worker soit provisionné automatiquement lors de mon onboarding.

---

## Epic 5 : Real-time Dashboard 📊
*Interface utilisateur de monitoring et de configuration.*

### User Stories :
- **US 5.1** : En tant qu'utilisateur, je veux voir l'état actuel de ma streak et l'historique des derniers pulses.
- **US 5.2** : En tant qu'utilisateur, je veux pouvoir changer mes préférences de Jitter et mes styles de messages de commit.
- **US 5.3** : En tant qu'utilisateur, je veux une interface fluide utilisant TanStack Query pour des mises à jour sans rechargement.
- **US 5.4** : En tant qu'utilisateur, je veux recevoir des alertes si mon worker rencontre une erreur de configuration.

---

[⬅️ Retour au Produit](index.md)
