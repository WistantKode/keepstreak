---
stepsCompleted: [1, 2, 3, 4, 7, 8, 9, 10, 11]
inputDocuments:
  - docs/architecture/redis-deep-dive.md
  - docs/architecture/system-overview.md
  - docs/onboarding/saas-workflow.md
  - docs/security/security-protocol.md
workflowType: 'prd'
lastStep: 11
---

# Product Requirements Document - KeepStreak

**Author:** Kali
**Date:** 2026-01-09T01:43:14+01:00

## Project Classification

**Technical Type:** saas_b2b (Plateforme de services développeurs)
**Domain:** Général (Productivité / Utilitaires Dev)
**Complexity:** Moyenne
**Project Context:** Brownfield - extension du brainstorming existant

## Executive Summary

KeepStreak est une plateforme d'orchestration distribuée conçue pour automatiser des workflows GitHub de manière ultra-sécurisée et décentralisée. Bien que son premier cas d'usage soit le maintien d'activité (streak), KeepStreak est avant tout une démonstration d'ingénierie logicielle complexe. En exploitant la robustesse de NestJS et une architecture de "Shadow Workers" via GitHub Actions, il résout le défi critique du stockage des secrets tiers ("Zero-Token Storage") dans les architectures SaaS modernes.

### Ce qui rend ce projet spécial

1. **Architecture "Zero-Token Storage" (Security-First)** : Une innovation de sécurité où le serveur central ne possède jamais les credentials GitHub de l'utilisateur. Le "handshake" est validé cryptographiquement (HMAC), éliminant tout risque de fuite de données massive.
2. **Ingénierie de Niveau Entreprise** : L'utilisation de NestJS et Turborepo ne vise pas la simplicité, mais la mise en œuvre de patterns professionnels (Injection de dépendances, DTOs, modules isolés), prouvant une capacité à concevoir des systèmes scalables.
3. **Moteur de Queue Résilient** : Gestion des jobs via Redis Streams pour garantir l'exécution même en cas de pic de charge ou de panne serveur.

## Success Criteria

### User Success (Sérénité & Confiance)
*   **Zero-Incident de Sécurité** : Aucun token GitHub n'est jamais compromis ou stocké sur le serveur central.
*   **Confiance Transparente** : L'utilisateur (toi ou d'autres devs) ressent une totale sérénité grâce à la visibilité claire sur le fonctionnement des "Shadow Workers".
*   **Configuration Sans Friction** : Un setup complet en moins de 3 minutes sans quitter le navigateur.

### Business & Career Success (Impact Recruteur)
*   **Visibilité Sociale** : Atteindre un seuil de stars/forks significatif démontrant l'intérêt de la communauté dev pour le projet.
*   **Preuve de Maîtrise** : Le projet est cité comme un point fort durant les entretiens techniques, validant ainsi ton expertise NestJS/Turborepo.
*   **Fiabilité Totale** : Disponibilité de la plateforme à 99.9% pour que les recruteurs puissent tester le dashboard à tout moment.

### Technical Success
*   **Orchestration Résiliente** : 100% des pulses programmés sont envoyés aux workers sans perte de données.
*   **Observabilité** : Mise en place d'un monitoring centralisé (logs/Redis) permettant d'identifier un problème avant qu'il n'affecte l'utilisateur.

## Product Scope (V1 Full Release)

*   **Système d'Onboarding Complet** : OAuth GitHub, création automatique du repo worker et injection des secrets.
*   **Dashboard de Monitoring** : Interface web (Next.js) permettant de piloter l'état des pulses, de voir l'historique et de modifier les préférences.
*   **Moteur de Pulse "Shadow"** : Orchestration robuste via NestJS et Redis pour déclencher les actions décentralisées.
*   **Sécurité Native** : Chiffrement AES-256-GCM et validation HMAC implémentés dès le premier jour.

## SaaS B2B Specific Requirements

### Technical Architecture Considerations
*   **Multi-tenancy Model** : Architecture à base de données unique avec isolation logique par `user_id`. Les ressources (repos, pulses) sont rattachées à un utilisateur unique.
*   **Modèle d'Authentification Hybride** :
    *   **Direct** : Login via GitHub OAuth (liaison automatique).
    *   **Social/Standard** : Login via Google, Apple ou Email. Si le compte n'est pas lié, un flux d'onboarding force la connexion à l'API GitHub pour activer les fonctionnalités de l'app.
*   **RBAC (Role-Based Access Control)** :
    *   **Role User** : Accès à ses propres pulses, son dashboard et ses paramètres de sécurité.
    *   **Role Admin (Kali)** : Accès aux métriques globales du système, monitoring de santé du Redis et gestion des utilisateurs.

### Implementation Specifics
*   **GitHub Auth Flow** : Implémentation d'un module d'échange de tokens sécurisé pour lier le profil utilisateur aux permissions de création de repo.
*   **Tenant Mapping** : Utilisation de guards NestJS pour garantir que chaque requête API respecte les frontières du "tenant" (utilisateur).

## Project Scoping & Phased Development

### V1 Strategy & Philosophy
**Approach** : "V1 Full Release" - Produit complet et professionnel dès le premier jour, axé sur la démonstration d'expertise technique (NestJS, Sécurité, Orchestration).
**MVP vs Full** : Rejet de la notion de MVP "maigre" au profit d'un produit fini et robuste.

### V1 Full Release Scope
**Core User Journeys Supported** :
*   Kali : Sérénité "Set & Forget".
*   Alex (Recruteur) : Validation technique via architecture et doc.
*   Résilience : Récupération automatique après panne.

**Must-Have Capabilities** :
*   **Shadow Infrastructure** : Orchestration Redis Streams + GitHub Action Workers.
*   **Security Protocol** : Architecture Zero-Token avec validation HMAC.
*   **Stealth Engine (Mode Invisible)** : Jitter temporel dynamique et messages de commits aléatoires pour simuler une activité humaine réelle.
*   **Professional Dashboard** : Interface de monitoring temps-réel (Next.js) avec gestion RBAC (Admin/User).

### Future Roadmap
**Phase 2 (Growth)** :
*   Multi-Provider : Support pour GitLab/Bitbucket.
*   Stats Avancées : Visualisation de l'impact sur le "Pulse" GitHub.
**Phase 3 (Expansion)** :
*   Social Integration : Notifications Slack/Discord/Telegram pour l'état de santé du système.

### Risk Mitigation Strategy
**Technical Risks** : Dépendance aux APIs GitHub. Mitigation : Architecture modulaire permettant de mettre à jour le worker indépendamment du cerveau central.
**Market/Career Risks** : Perception de "script simple". Mitigation : Mise en avant du PRD et de la documentation d'architecture technique complexe.

## Functional Requirements

### 🔐 Authentification & Sécurité (Contrat "Zero-Token")
*   **FR1** : Un utilisateur peut se connecter via GitHub OAuth ou des méthodes sociales (Google/Apple).
*   **FR2** : Un utilisateur non-relié à GitHub peut lier son profil manuellement via un flux dédié.
*   **FR3** : Le système peut effectuer un handshake cryptographique avec le worker sans jamais stocker le token GitHub en clair sur le serveur central.
*   **FR4** : Le système peut chiffrer/déchiffrer les données sensibles via AES-256-GCM.
*   **FR19** : Le système doit implémenter un Rate-Limiting par utilisateur pour éviter les saturations de file d'attente.
*   **FR20** : Le système doit inclure un mécanisme de TTL sur chaque signal HMAC pour empêcher les attaques par rejeu.
*   **FR21** : Le système doit garantir que les logs d'exécution ne contiennent aucune donnée confidentielle.
*   **FR22** : Le système doit imposer une rotation régulière des secrets internes de chiffrement.

### 🚀 Configuration & Onboarding
*   **FR5** : Un utilisateur peut autoriser l'app à créer un repo privé spécifique (keepstreak-pulse) sur son compte.
*   **FR6** : Le système peut injecter automatiquement les GitHub Actions et les secrets nécessaires dans le repo worker.
*   **FR7** : Un utilisateur peut définir ses préférences de "Pulse" (activation/désactivation).

### 🕶️ Stealth Engine (Mode Invisible)
*   **FR8** : Le système peut calculer des délais aléatoires (jitter) pour chaque pulse afin d'éviter les routines fixes.
*   **FR9** : Le système peut générer des messages de commits variés et contextuels.
*   **FR10** : Le système peut simuler des modifications de fichiers minimes.

### ⚙️ Orchestration & Résilience (Shadow Workers)
*   **FR11** : Le système peut planifier et envoyer des signaux de déclenchement vers Redis Streams.
*   **FR12** : Le système peut détecter un pulse manqué et déclencher un mécanisme de récupération.
*   **FR13** : Le système peut gérer la file d'attente des jobs pour garantir l'ordre d'exécution.

### 📊 Dashboard & Monitoring
*   **FR14** : Un utilisateur peut visualiser l'état actuel de son "Pulse" (Actif/Inactif/Prévu).
*   **FR15** : Un utilisateur peut consulter l'historique des pulses passés.
*   **FR16** : Un administrateur (Kali) peut visualiser la santé globale du système, y compris la santé de Redis et les erreurs critiques.
*   **FR23** : Le système doit garantir l'isolation stricte des données (Data Segregation) entre l'admin et les utilisateurs.
*   **FR24** : Le système doit générer des Logs d'Audit immuables pour chaque action sensible.

### 🔔 Alertes & Notifications
*   **FR17** : Un utilisateur peut recevoir une confirmation (optionnelle) après l'exécution réussie d'un pulse.
*   **FR18** : Le système peut notifier l'utilisateur en cas d'échec critique du worker.
