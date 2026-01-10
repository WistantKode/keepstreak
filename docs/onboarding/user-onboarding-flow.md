# Flux d'Onboarding SaaS

Le succès de **KeepStreak** repose sur une interface "frictionless" où l'aspect technique est totalement géré en arrière-plan.

## 1. Connexion & Handshake Magique
- **Dashboard Elite** : L'utilisateur se connecte via GitHub OAuth. 
- **Autorisation Éphémère** : Nous demandons une autorisation temporaire pour configurer l'infrastructure de pulse.

## 2. Provisioning Shadow (Automatisé)
Dès validation, le Cerveau orchestre les opérations suivantes sans aucune intervention manuelle :
1. **Creation du Repo Pulse** : Un repository privé `keepstreak-pulse` est créé instantanément.
2. **Injection du Moteur** : Déploiement automatique du `.github/workflows/pulse.yml` (le Shadow Worker).
3. **Sécurisation des Secrets** : Injection cryptographique des secrets `KEEPSTREAK_CLIENT_ID` et `HANDSHAKE_SECRET`.

## 3. Configuration du Dashboard
L'utilisateur est redirigé vers son dashboard personnel où il peut :
- **Activer/Désactiver** la streak globale.
- **Régler le Jitter** : Fréquence et plages horaires d'activité.
- **Personnaliser les messages** : Style de commits (pro, casual, emoji).

## 4. Le Premier "Pulse"
Une fois validé, l'API planifie immédiatement une tâche de test dans Redis pour vérifier que le worker GitHub Action est capable de communiquer avec le Cerveau.

## 5. Maintenance Continue
- **Mises à jour** : Les Shadow Workers vérifient la version du script à chaque exécution. Si une nouvelle version est disponible sur le Cerveau, ils se mettent à jour automatiquement.
- **Monitoring** : Le Dashboard affiche les derniers commits réussis et les erreurs de handshake éventuelles.

---
[⬅️ Retour à l'Onboarding](index.md)
