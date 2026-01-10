# Redis Streams & Orchestration

## Pourquoi Redis Streams ?
Pour **KeepStreak**, nous avons choisi Redis Streams au lieu d'une simple queue (LIST) ou de Pub/Sub pour plusieurs raisons critiques :
1. **Persistance** : Les messages restent dans le flux jusqu'à ce qu'ils soient explicitement acquittés.
2. **Consumer Groups** : Permet de distribuer les tâches de déclenchement des workers sur plusieurs instances de notre API sans doublons.
3. **PEL (Pending Entries List)** : Permet de détecter les tâches qui ont commencé mais n'ont jamais fini (ex: crash du serveur).

## Architecture du Flux `pulse_commands`

### 1. Ingestion (Producer)
Lorsqu'un commit est dû, l'API NestJS injecte une entrée :
```bash
XADD pulse_commands * repo_id "123" user_id "456" action "COMMIT" jitter "15m"
```

### 2. Distribution (Consumer Groups)
Plusieurs instances de l'API (ou un service dédié) écoutent le stream :
```bash
XREADGROUP GROUP workers instance_1 BLOCK 2000 STREAMS pulse_commands >
```

### 3. Cycle de Vie d'une Tâche
- **ACK** : Une fois que le signal de déclenchement a été envoyé à GitHub (via `workflow_dispatch`), l'API envoie un `XACK`.
- **Retry Logic** : Si une entrée reste dans la PEL plus de 5 minutes, elle est réclamée par une autre instance (`XCLAIM`) pour être re-tentée.

## Gestion du Jitter et Scheduling
Au lieu de déclencher à une heure fixe (ex: 14h00), l'API calcule une fenêtre de temps aléatoire. La tâche est placée dans Redis avec un paramètre `scheduled_at`. Un worker de l'API vérifie périodiquement les tâches prêtes à être expédiées.

---
[⬅️ Retour aux Systèmes Distribués](index.md)
