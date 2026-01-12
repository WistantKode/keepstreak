# Environment Setup 🔧

Ce guide décrit la configuration de l'environnement de développement local et la gestion des secrets.

---

## Prérequis

| Outil | Version | Installation |
|-------|---------|--------------|
| **Node.js** | 20+ | `nvm install 20` |
| **pnpm** | 9+ | `corepack enable pnpm` |
| **Docker** | 24+ | docs.docker.com |
| **Git** | 2.40+ | - |

---

## Installation Locale

```bash
# 1. Cloner le repo
git clone git@github.com:WistantKode/keepstreak.git
cd keepstreak

# 2. Installer les dépendances
pnpm install

# 3. Configurer les variables d'environnement
cp .env.example .env.local

# 4. Générer le client Prisma
pnpm db:generate

# 5. Lancer en mode développement
pnpm dev
```

---

## Variables d'Environnement

### `.env.local` (Développement)

```env
# Database
DATABASE_URL="postgresql://user:password@localhost:5432/keepstreak?schema=public"

# Redis
REDIS_URL="redis://localhost:6379"

# Auth
JWT_SECRET="dev-jwt-secret-change-in-production"
GITHUB_CLIENT_ID="Iv1.xxxxxxxxxxxx"
GITHUB_CLIENT_SECRET="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Encryption
ENCRYPTION_KEY="32-byte-hex-key-for-aes-256-gcm"
HMAC_SECRET="hmac-secret-for-worker-handshake"

# API
API_PORT=3001
NEXT_PUBLIC_API_URL="http://localhost:3001"
```

---

## Services Locaux (Docker)

Pour développer sans dépendances cloud:

```bash
# Lancer PostgreSQL et Redis en local
docker compose -f docker-compose.dev.yml up -d
```

**`docker-compose.dev.yml`:**

```yaml
version: '3.8'
services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: keepstreak
      POSTGRES_PASSWORD: localdev
      POSTGRES_DB: keepstreak
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

---

## Gestion des Secrets (Production)

### Génération des Clés

```bash
# JWT Secret (256 bits)
openssl rand -base64 32

# AES-256-GCM Key (32 bytes hex)
openssl rand -hex 32

# HMAC Secret
openssl rand -base64 32
```

### Rotation des Clés

1. **Générer** une nouvelle clé
2. **Ajouter** la nouvelle clé en parallèle (dual-key mode)
3. **Migrer** les données chiffrées
4. **Supprimer** l'ancienne clé

---

## IDE Configuration

### VSCode Extensions

- ESLint
- Prettier
- Prisma
- Docker
- GitLens

### `.vscode/settings.json`

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "typescript.preferences.importModuleSpecifier": "relative"
}
```

---

[⬅️ Retour au Build](index.md)
