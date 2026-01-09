# Protocole de Sécurité Militaire

## Principes Fondamentaux
1. **Zero-Token Storage** : Le VPS ne stocke jamais les tokens GitHub des utilisateurs tiers.
2. **Anonymisation** : Utilisation d'UUIDs pour identifier les sessions de commit.
3. **Handshake Sécurisé** : Validation des requêtes via HMAC SHA-256.

## Protection des Données
Les préférences utilisateurs stockées dans la base de données sont chiffrées en **AES-256-GCM** au repos.

## Isolation
Chaque commit est exécuté dans l'environnement isolé de la GitHub Action de l'utilisateur, limitant la portée d'une faille sur le serveur central.
