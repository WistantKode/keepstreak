# GitHub Workers & Stealth Engine

## Introduction
Le **Shadow Worker** est le composant décentralisé de **KeepStreak**. Il s'exécute sous la forme d'un service périodique au sein d'une GitHub Action sur le repository privé de l'utilisateur.

## Moteur Stealth (Discrétion)
Le but du moteur Stealth est d'éviter la détection par les filtres "anti-bot" et de donner une apparence humaine à l'activité générée.

### Simulation de Comportement
- **Jitter de Timing** : L'heure d'exécution n'est jamais fixe. Elle est décalée de +/- X minutes par rapport à la consigne centrale.
- **Variabilité des Messages** : Le moteur choisit parmi plusieurs templates de messages de commit (Style Pro, Fixes mineurs, Emojis) basés sur les préférences du dashboard.
- **Payloads Dynamiques** : Chaque commit modifie un fichier de "Pulse" avec des données chiffrées uniques pour garantir un hash de commit différent à chaque fois.

## Logique de l'Action GitHub
Le fichier `.github/workflows/pulse.yml` contient la logique suivante :

1. **Trigger** : Déclenché par `workflow_dispatch` via l'API GitHub depuis notre Cerveau Central.
2. **Environment** : Récupération des secrets `HANDSHAKE_SECRET` et `CLIENT_ID`.
3. **Instruction Fetch** :
   - Requête vers l'API KeepStreak.
   - Handshake HMAC pour valider la provenance.
4. **Execution** :
   - Si une tâche `COMMIT` est en attente : Exécution du commit avec les paramètres reçus.
   - Si une tâche `UPDATE` est en attente : Mise à jour du script du worker lui-même.
5. **Reporting** : Envoi d'un signal de succès (ACK) vers l'API.

## Sécurité du Worker
Le worker ne possède que les droits d'écriture sur son propre repository. Il n'a aucun accès aux données des autres utilisateurs de la plateforme. Toute communication avec le Cerveau est signée et horodatée pour empêcher les attaques par rejeu.

---
[⬅️ Retour aux Systèmes Distribués](file:///home/kali-root/Dev/PersonnalProjects/KeepStreak/docs/architecture/distributed-systems/index.md)
