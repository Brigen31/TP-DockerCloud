# Docker Swarm Orchestration

Ce document explique comment mettre en place et déployer l'application sur un cluster Docker Swarm.

## 1. Initialisation du mode Swarm

Si ce n'est pas déjà fait, initialisez Docker Swarm sur votre machine (manager node) :

```bash
docker swarm init
```
*Note : Si vous avez plusieurs interfaces réseau, vous devrez peut-être spécifier l'IP à utiliser avec `--advertise-addr <IP>`.*

## 2. Construction des images

Pour que le Swarm puisse trouver les images, il faut d'abord les construire. Nous utilisons le fichier `swarm.yml` qui définit les noms d'images.

```bash
docker compose -f swarm.yml build
```

*Note : Dans un environnement de production multi-nœuds, ces images devraient être poussées vers un registre (Docker Hub, registre privé, etc.) accessible par tous les nœuds.*

## 3. Déploiement de la Stack

Déployez la "stack" applicative nommée `tp-app`. Cela créera les services avec les 3 réplicas demandés.

```bash
docker stack deploy -c swarm.yml tp-app
```

## 4. Vérification et Gestion

### Voir la liste des services
```bash
docker service ls
```
Vous devriez voir `tp-app_backend`, `tp-app_frontend` et `tp-app_webserver` avec 3/3 réplicas chacun.

### Voir les tâches (conteneurs) d'un service
```bash
docker service ps tp-app_backend
```

### Voir les logs d'un service
```bash
docker service logs -f tp-app_backend
```

### Supprimer la stack
Pour arrêter et supprimer tous les services :
```bash
docker stack rm tp-app
```

### Quitter le mode Swarm
```bash
docker swarm leave --force
```
