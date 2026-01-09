# Sécurité & Protocoles

La sécurité est le pilier central de **KeepStreak**. Notre approche repose sur la minimisation des privilèges et le chiffrement de bout en bout.

## Sommaire

### 🛡️ [Protocoles de Sécurité](security-protocols.md)
Détails techniques du handshake HMAC, de l'isolation des tenants et du chiffrement AES-256-GCM.

### 🔑 Gestion des Identités
- GitHub OAuth 2.0 (Scopes minimaux : `repo`, `workflow`).
- Sessions sécurisées via Auth.js.

### 🔒 Zero-Token Storage
Explication de comment nous évitons de stocker des tokens persistants côté serveur central grâce aux GitHub App secrets et au handshake dynamique.

---
[⬅️ Retour à l'accueil](../index.md)
