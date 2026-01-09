# Workflow d'Onboarding SaaS

## Expérience Utilisateur
Le but est d'éliminer toute friction technique. L'utilisateur ne doit jamais quitter l'interface web.

## Étapes Automatisées
1. **Connexion GitHub OAuth** : Récupération des permissions minimales.
2. **Setup Automatique** :
    - Création du repository privé `keepstreak-pulse`.
    - Injection du fichier `.github/workflows/pulse.yml`.
    - Configuration automatique des secrets du repo.
3. **Activation** : L'utilisateur définit ses préférences sur le dashboard et valide.

## Maintenance
Le script d'Action GitHub fait régulièrement un "check" vers le serveur central pour récupérer les mises à jour de logique sans intervention du user.
