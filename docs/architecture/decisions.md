---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
inputDocuments:
  - docs/prd.md
  - docs/architecture/system-overview.md
  - docs/architecture/redis-deep-dive.md
  - docs/security/security-protocol.md
  - docs/onboarding/saas-workflow.md
workflowType: 'architecture'
project_name: 'KeepStreak'
user_name: 'Kali'
date: '2026-01-09T02:45:06+01:00'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
- **Orchestration décentralisée** : Déclenchement de GitHub Actions (workers) via un système de queue (Redis Streams).
- **Sécurité "Zero-Token Storage"** : Handshake HMAC SHA-256 pour éviter le stockage des tokens tiers sur le serveur central.
- **Onboarding Automatisé** : OAuth GitHub et auto-provisioning de repos workers.
- **Stealth Engine** : Jitter temporel et messages de commits aléatoires pour simuler l'activité humaine.
- **Observabilité** : Dashboard Next.js avec RBAC (Admin/User) pour le monitoring temps-réel.

**Non-Functional Requirements:**
- **Résilience** : Gestion des pannes via Consumer Groups Redis (PEL/nack).
- **Sécurité Militaire** : Chiffrement AES-256-GCM au repos et isolation stricte des données.
- **Scalabilité** : Capacité à traiter des milliers de "Pulse Checks" par seconde (Redis performance).

**Scale & Complexity:**
- **Domaine Principal** : API / Backend (Orchestration distribuée) & Frontend (Monitoring).
- **Niveau de Complexité** : Élevé (Multi-ténance, cryptographie, gestion d'état distribué).
- **Composants Architecturaux Estimés** : API Core (NestJS), Queue (Redis), Workers (GH Actions), Dashboard (Next.js), Database (PostgreSQL/MongoDB - à confirmer).

### Technical Constraints & Dependencies
- Dépendance forte aux APIs GitHub et aux limites de rate-limiting associées.
- Nécessité d'un environnement VPS avec Redis persistant.
- Utilisation de Turborepo pour la gestion du monorepo.

### Cross-Cutting Concerns Identified
- **Sécurité** : Validation systématique de l'identité des workers via HMAC.
- **Audit & Logging** : Logs d'audit immuables pour les actions sensibles.
- **Isolation** : Isolation logique par `user_id` à tous les niveaux (DB, Queue, Workers).

## Starter Template Evaluation

### Primary Technology Domain

**Full-stack SaaS Monorepo** basé sur l'orchestration distribuée.

### Starter Options Considered

1.  **Existing Custom Turborepo** (Actuel) : Configuration déjà en place avec NestJS 11 et Next.js 16.
2.  **create-turbo** (Standard) : Le point de départ officiel de Vercel. Plus minimal que l'existant.
3.  **Specialized Starters (ex: nestjs-turbo)** : Offre des patterns plus avancés pour l'intégration NestJS.

### Selected Starter: Existing Custom Turborepo

**Rationale for Selection:**
L'infrastructure actuelle est déjà configurée avec les dernières versions (NestJS 11, Next.js 16) et respecte la structure monorepo (apps/packages) nécessaire. Elle inclut déjà des configurations partagées (ESLint, TS) et une base UI, ce qui permet de se concentrer immédiatement sur la logique métier complexe (**Shadow Workers/Redis**).

**Initialization Command:**
Utilisation de la structure existante. Pour ajouter de nouveaux packages :
```bash
pnpm turbo gen package
```

**Architectural Decisions Provided by Starter:**

- **Language & Runtime** : TypeScript 5.9 + Node.js 18+.
- **Styling Solution** : CSS Modules / Global CSS (Next.js standard).
- **Build Tooling** : Turborepo pour l'orchestration des builds et du cache.
- **Testing Framework** : Jest (déjà configuré dans `apps/api`).
- **Code Organization** : Architecture Monorepo avec séparation stricte entre `apps` (API/Web) et `packages` (Shared logic/UI).
- **Development Experience** : Support complet du Hot Reloading et du typage strict via TypeScript.

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
- **Database & ORM** : Choix de PostgreSQL (Neon) et Prisma v7.
- **Security Protocol** : Validation HMAC SHA-256 pour les Shadow Workers.
- **Authentication** : Auth.js (NextAuth) pour le dashboard web.

**Important Decisions (Shape Architecture):**
- **State Management** : TanStack Query pour la gestion des états asynchrones au frontend.
- **Queue Engine** : Redis Streams pour l'orchestration des pulses.

### Data Architecture

- **Database** : **PostgreSQL** hébergé sur **Neon**.
- **ORM** : **Prisma v7** (stable).
- **Configuration** : Utilisation de `DATABASE_URL` (poolé) et `DIRECT_URL` (direct) pour les migrations. Le driver adapter `@prisma/adapter-neon` sera utilisé pour optimiser les performances serverless.
- **Rationale** : Intégrité des données, facilité de gestion des relations complexes et excellentes performances avec NestJS.

### Authentication & Security

- **Web Dashboard** : **Auth.js** (NextAuth) avec le provider GitHub OAuth.
- **Worker Handshake** : **HMAC SHA-256**. L'API NestJS validera chaque requête du worker via une signature cryptographique temporisée (TTL).
- **Data Encryption** : **AES-256-GCM** pour les secrets stockés en DB.
- **Rationale** : Sécurité "Zero-Token Storage" garantissant que les accès GitHub de l'utilisateur ne sont jamais compromis.

### API & Communication Patterns

- **API Style** : **REST** avec NestJS (Modules, Controllers, Services).
- **Documentation** : **Swagger/OpenAPI** (automatisé par NestJS).
- **Real-time Monitoring** : **Server-Sent Events (SSE)** ou **WebSockets** via NestJS pour le feedback dashboard.

### Frontend Architecture

- **State Management** : **TanStack Query v5+**.
- **Styling** : **Tailwind CSS** (pour la cohérence UI/UX) ou CSS Modules (existant).
- **Rationale** : Simplicité de synchronisation entre le serveur et le client, performance optimale du rendu.

### Infrastructure & Deployment

- **Backend** : VPS **Hostinger** (NestJS + Redis).
- **Frontend** : **Vercel** ou Hostinger.
- **Database** : **Neon** (Postgres Serverless).
- **CI/CD** : **GitHub Actions** pour le déploiement automatisé et les tests.

## Implementation Patterns & Consistency Rules

### Pattern Categories Defined

**Critical Conflict Points Identified:**
- **Shared DTOs** : Nécessité de partager les types entre l'API et le Web pour éviter les désynchronisations.
- **Naming Mismatch** : Risque de mélange entre `snake_case` (DB) et `camelCase` (API) sans couche de transformation claire.

### Naming Patterns

- **Database Naming Conventions** : `snake_case` (ex: `pulse_history`, `user_id`).
- **API Naming Conventions** : `camelCase` pour les propriétés JSON, `kebab-case` pour les routes (ex: `/api/pulse-settings`).
- **Code Naming Conventions** : `PascalCase` pour les composants UI et classes, `camelCase` pour les fonctions et variables.
- **File Naming** : `kebab-case` (ex: `pulse-card.tsx`, `auth.service.ts`).

### Structure Patterns

- **NestJS (API)** : Architecture par domaine (ex: `src/pulses/`, `src/auth/`). Chaque domaine contient ses propres controllers, services et modules.
- **Next.js (Web)** : Utilisation de l'App Router. Logique métier regroupée par features dans `app/features/`.
- **Packages Partagés** : `packages/api-client` (généré), `packages/types` (DTOs partagés), `packages/ui` (composants de base).
- **Tests** : Co-localisation des tests unitaires (`.spec.ts`) avec le code source.

### Format Patterns

- **API Response Formats** : Toutes les réponses sont enveloppées : `{ "data": T, "error": string | null }`.
- **Data Exchange Formats** : ISO 8601 pour les dates. `true`/`false` pour les booléens.

### Communication Patterns

- **Event System Patterns** : Naming des événements en `domain.action` (ex: `pulse.triggered`).
- **State Management Patterns** : Immuabilité stricte. Utilisation de **TanStack Query** pour la synchronisation de l'état asynchrone.

### Process Patterns

- **Error Handling Patterns** : Exceptions personnalisées dans NestJS avec un Filter d'exception global pour standardiser le format de sortie.
- **Loading State Patterns** : Utilisation de `isPending` / `isLoading` fournis par TanStack Query au niveau des composants.

### Enforcement Guidelines

**All AI Agents MUST:**
- Utiliser le package partagé pour les types API (DTOs).
- Respecter la transformation automatique `snake_case` <-> `camelCase` entre le repo Prisma et l'API.

**Pattern Examples:**
- **Good** : `const pulseId = '...';` (Code) -> `pulse_id` (DB).
- **Anti-Pattern** : `const user_id = '...';` (Code) ou `/api/getPulse` (Route API).

## Project Structure & Boundaries

### Complete Project Directory Structure

```text
KeepStreak/
├── apps/
│   ├── api/                # NestJS 11 (Cerveau)
│   │   ├── src/
│   │   │   ├── auth/       # Gestion OAuth & Sessions
│   │   │   ├── pulses/     # Orchestration Redis & Stealth Engine
│   │   │   ├── users/      # Profils & Sécurité AES
│   │   │   ├── worker/     # Handshake HMAC & Provisioning
│   │   │   └── common/     # Guards, Interceptors, Filters
│   │   └── test/
│   ├── web/                # Next.js 16 (Dashboard)
│   │   ├── app/
│   │   │   ├── dashboard/  # Monitoring temps-réel
│   │   │   ├── settings/   # Prefs de pulse
│   │   │   └── auth/       # Flux Login GitHub
│   │   └── components/     # UI spécifique au métier
│   └── docs/               # documentation technique interne
├── packages/
│   ├── db/                 # Prisma Schema & Client partagé
│   ├── types/              # DTOs & Interfaces transverses
│   ├── ui/                 # Design System (React 19)
│   ├── config-eslint/      # Règles de linting partagées
│   └── config-typescript/  # Conf TS partagée
├── turbo.json              # Orchestration Turborepo
└── pnpm-workspace.yaml     # Gestion des packages
```

### Architectural Boundaries

**API Boundaries :**
- L'API NestJS (`apps/api`) est le point d'entrée unique pour la modification des états (pulses, réglages).
- Communication Type-safe via des DTOs partagés (`packages/types`).

**Component Boundaries :**
- Les composants métier complexes (ex: `PulseGraph`) vivent dans `apps/web`.
- Les composants UI atomiques (ex: `Button`, `Input`) vivent dans `packages/ui`.

**Data Boundaries :**
- Prisma Client est centralisé dans `packages/db` pour garantir une source de vérité unique pour le schéma PostgreSQL.

### Requirements to Structure Mapping

**Shadow Workers & Orchestration :**
- Logique de queue : `apps/api/src/pulses/`
- Handshake HMAC : `apps/api/src/worker/`

**Stealth Engine :**
- Calcul du jitter & messages aléatoires : `apps/api/src/pulses/stealth/`

**Onboarding & OAuth :**
- Flux GitHub OAuth : `apps/api/src/auth/`
- Provisioning Repo : `apps/api/src/worker/provisioning/`

### Integration Points

**Internal Communication :**
- Le Dashboard Web appelle l'API NestJS via des hooks **TanStack Query**.
- L'API NestJS pousse les tâches vers **Redis Streams**.

**External Integrations :**
- GitHub API : Utilisée par `apps/api` pour l'onboarding.
- GitHub Actions : Déclenchées par les signaux envoyés par le Cerveau.

**Data Flow :**
1. User -> Web (Settings) -> API (Postgres)
2. API (Scheduler) -> Redis Streams -> API (Worker Dispatcher) -> GitHub Action

## Architecture Validation

### Requirements Coverage Map

| Requirement | Architectural Component | Status |
| :--- | :--- | :--- |
| **Shadow Workers** | NestJS Worker Module + GH Actions | ✅ Covered |
| **Zero-Token Storage** | Handshake HMAC + AES Encryption | ✅ Covered |
| **Stealth Engine** | Redis Jitter Logic + Commit Message Gen | ✅ Covered |
| **Dashboard** | Next.js 16 + TanStack Query | ✅ Covered |
| **Onboarding** | GitHub OAuth + Repo Provisioning | ✅ Covered |
| **Multi-tenancy** | Logique `user_id` + Prisma Isolation | ✅ Covered |

### Technical Coherence Check

- **Sécurité** : Le protocole HMAC garantit l'intégrité du déclenchement sans exposer de secrets persistants. L'usage d'AES-256-GCM protège les préférences utilisateur en base.
- **Résilience** : Redis Streams avec Consumer Groups permet de rejouer des pulses échoués et de gérer la charge de manière asynchrone.
- **Performance** : Neon (Serverless Postgres) et Redis offrent une latence minimale pour les "Pulse Checks".

### Implementation Readiness

- [x] Schéma de base défini (via Prisma).
- [x] Conventions de nommage fixées.
- [x] Structure monorepo Turborepo active.
- [x] Choix technologiques stables (Nest 11, Next 16).

**Gaps identifiés :**
- La rotation des secrets (FR22) devra être gérée périodiquement via un cron job NestJS.

**Note:** Validation terminée. Nous sommes prêts à finaliser le document et à préparer le plan d'implémentation.

## Architecture Completion Summary

### Workflow Completion

**Architecture Decision Workflow:** COMPLETED ✅
**Total Steps Completed:** 8
**Date Completed:** 2026-01-09
**Document Location:** docs/architecture.md

### Final Architecture Deliverables

**📋 Complete Architecture Document**
- Toutes les décisions architecturales sont documentées avec des versions spécifiques.
- Les patterns d'implémentation garantissent la cohérence entre les agents AI.
- Structure complète du projet avec répertoires et fichiers clés.
- Mapping des besoins (PRD) vers l'architecture.
- Validation confirmant la cohérence et l'exhaustivité.

**🏗️ Implementation Ready Foundation**
- **10+** décisions architecturales majeures enregistrées.
- **5+** patterns d'implémentation définis.
- Structure Monorepo prête pour le développement.
- 100% des besoins fonctionnels supportés.

**📚 AI Agent Implementation Guide**
- Stack technique vérifiée (Nest 11, Next 16, Prisma 7).
- Règles de cohérence pour éviter les conflits (naming, structure).
- Frontières d'intégration et standards de communication définis.

### Implementation Handoff

**Pour les agents AI :**
Ce document d'architecture est votre guide complet pour l'implémentation de **KeepStreak**. Suivez scrupuleusement les décisions, patterns et structures documentés.

**Séquence de Développement Recommandée :**
1. Configurer l'environnement de développement selon l'architecture (Neon, Redis).
2. Implémenter le schéma Prisma partagé dans `packages/db`.
3. Développer les briques de base de sécurité (HMAC/AES) dans l'API.
4. Construire les fonctionnalités métier (Pulses, Stealth Engine) en suivant les patterns établis.

---

**Architecture Status:** READY FOR IMPLEMENTATION ✅
