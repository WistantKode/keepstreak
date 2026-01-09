# Protocoles de Sécurité & Handshake

## 1. Zero-Token Storage
Le principe "Zero-Token Storage" garantit que **KeepStreak** ne stocke jamais de tokens GitHub persistants (PAT) sur son infrastructure centrale.

### Fonctionnement :
1. L'utilisateur installe l'application KeepStreak sur son repository.
2. Un secret `GH_APP_TOKEN` est stocké directement dans les **GitHub Actions Secrets** du repo client.
3. Le serveur central ne connaît que l'ID du repo et l'ID d'installation.

## 2. Le Handshake HMAC SHA-256
Pour s'assurer qu'une requête de "Pulse" provient bien de notre Cerveau et n'est pas falsifiée par un tiers :

- **Secret Partagé** : Chaque utilisateur dispose d'une clé secrète AES générée à l'onboarding et stockée de manière chiffrée en DB.
- **Payload** : `timestamp + repo_id + action_type`.
- **Signature** : Le Cerveau génère un hash HMAC SHA-256 du payload avec la clé secrète.
- **Vérification** : Le Shadow Worker reçoit le payload et la signature, recalcule le hash localement et rejette la commande si les signatures ne correspondent pas.

## 3. Chiffrement AES-256-GCM
Les données sensibles (clés privées, préférences de messages de commit) sont chiffrées en base de données PostgreSQL via l'algorithme AES-256-GCM (Galois/Counter Mode), offrant à la fois la confidentialité et l'authenticité.

- **KMS local** : Une Master Key (stockée en variable d'environnement sur le Cerveau) est utilisée pour dériver les clés par utilisateur.

## 4. Isolation des Tenants
L'isolation est logicielle et renforcée par Prisma. Chaque requête API et chaque accès DB est filtré systématiquement par le `user_id` de la session OAuth active.

---
[⬅️ Retour à la Sécurité](file:///home/kali-root/Dev/PersonnalProjects/KeepStreak/docs/security/index.md)
