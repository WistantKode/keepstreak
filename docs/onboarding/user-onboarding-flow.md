# Flux d'Onboarding SaaS

Le succès de **KeepStreak** repose sur une interface "frictionless" où l'aspect technique est totalement géré en arrière-plan.

## 1. Phase de Découverte & Connexion
- **Landing Page** : Présentation de la valeur ajoutée (streak invisible, sécurité).
- **GitHub OAuth** : L'utilisateur se connecte. Nous demandons l'accès aux repositories et aux workflows.

## 2. Provisioning Automatique
Dès la connexion, le Cerveau exécute les opérations suivantes via l'API GitHub :
1. **Repository Check** : Vérifie si le repo `keepstreak-pulse` existe déjà.
2. **Creation (si besoin)** : Crée un repository privé pour l'utilisateur.
3. **Infrastructure Injection** :
    - Injection du fichier `.github/workflows/pulse.yml` (le moteur du worker).
    - Configuration des **Repository Secrets** : `KEEPSTREAK_CLIENT_ID` et `HANDSHAKE_SECRET`.

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
[⬅️ Retour à l'Onboarding](file:///home/kali-root/Dev/PersonnalProjects/KeepStreak/docs/onboarding/index.md)
