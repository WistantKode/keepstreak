# Monorepo Management 📦

Ce document décrit l'organisation du monorepo KeepStreak avec Turborepo et pnpm.

---

## Structure du Monorepo

```
├── apps/
│   ├── api/          # NestJS 11 - Core API ("Le Cerveau")
│   └── web/          # Next.js 16 - Dashboard utilisateur
├── packages/
│   ├── db/           # Prisma schema + client
│   ├── types/        # DTOs partagés
│   ├── eslint-config/# Configuration ESLint
│   └── tsconfig/     # Configurations TypeScript
├── turbo.json        # Configuration Turborepo
├── pnpm-workspace.yaml
└── package.json
```

---

## Configuration Turborepo

**`turbo.json`:**

```json
{
  "$schema": "https://turbo.build/schema.json",
  "globalDependencies": ["**/.env.*local"],
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**", "!.next/cache/**"]
    },
    "lint": {
      "dependsOn": ["^lint"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    },
    "test": {
      "dependsOn": ["build"],
      "inputs": ["src/**/*.tsx", "src/**/*.ts", "test/**/*.ts"]
    }
  }
}
```

---

## Commandes Principales

| Commande | Description |
|----------|-------------|
| `pnpm dev` | Démarre tous les apps en mode développement |
| `pnpm build` | Build toutes les apps et packages |
| `pnpm lint` | Lint le code de tous les workspaces |
| `pnpm test` | Exécute les tests |
| `pnpm db:generate` | Génère le client Prisma |
| `pnpm db:push` | Push le schema vers Neon |

---

## Gestion des Dépendances

### Ajouter une dépendance à un workspace

```bash
# Ajouter à l'API
pnpm add lodash --filter @keepstreak/api

# Ajouter au package shared types
pnpm add zod --filter @keepstreak/types

# Dépendance de développement root
pnpm add -D prettier -w
```

### Dépendances Internes

Les packages internes sont référencés avec le préfixe `workspace:*`:

```json
{
  "dependencies": {
    "@keepstreak/db": "workspace:*",
    "@keepstreak/types": "workspace:*"
  }
}
```

---

## Cache & Performance

Turborepo utilise un cache local par défaut. Pour le cache distant (CI):

```bash
# Activer Remote Caching (Vercel)
npx turbo login
npx turbo link
```

---

[⬅️ Retour au Build](index.md)
