# Deep Dive : Redis Streams & Jitter

## Rôle de Redis
Redis agit comme le centre nerveux de KeepStreak, gérant la file d'attente des commits de manière asynchrone et résiliente.

## Gestion du Jitter
Pour chaque utilisateur, un délai aléatoire ("Jitter") est calculé par rapport à sa heatmap habituelle. 
- **Message Redis** : `{ "id": "uuid", "target": "2026-01-09T23:45:00", "payload": "..." }`

## Résilience (Consumer Groups)
- Utilisation des **Consumer Groups** pour garantir qu'aucun commit n'est perdu en cas de crash d'un worker.
- Si un worker périt, le message est ré-assigné via la PEL (Pending Entries List).

## Performance
Sur Hostinger, Redis permet de traiter 5000+ requêtes de "Pulse Check" par seconde avec une consommation CPU minimale.
