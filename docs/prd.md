---
stepsCompleted: [1, 2]
inputDocuments:
  - docs/architecture/redis-deep-dive.md
  - docs/architecture/system-overview.md
  - docs/onboarding/saas-workflow.md
  - docs/security/security-protocol.md
workflowType: 'prd'
lastStep: 2
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
