# TP - DOCKER CLOUD

## Sommaire
- [COMMENCER](#commencer)
- [DOCUMENTATIONS](#documentations)
- [SCHÉMA DE COMMUNICATION](#schéma-de-communication)

## GET STARTED

Ce projet fonctionne en utilisant Docker Compose. Assurez-vous d'avoir Docker installé sur votre machine.

### Prérequis
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

### Lancer le Projet

Pour construire et démarrer l'application, exécutez la commande suivante dans le répertoire racine :

```bash
docker-compose up -d --build
```

Une fois les conteneurs opérationnels, vous pouvez accéder à l'application via :

- **Application Web :** [http://localhost](http://localhost)
- **Vérification de Santé de l'API :** [http://localhost/api/health](http://localhost/api/health)

### Arrêter le Projet

Pour arrêter les conteneurs :

```bash
docker-compose down
```

---

## DOCUMENTATIONS

Ce projet se compose de trois microservices principaux orchestrés par Docker Compose.

### 1. Webserver (Reverse Proxy)
- **Dossier :** `src/webserver`
- **Port :** Exposé sur le port `80`
- **Technologie :** Nginx
- **Rôle :** Agit comme le point d'entrée pour tout le trafic. Il route les requêtes vers le service approprié :
  - Les requêtes commençant par `/api/` sont transmises au service **Backend**.
  - Toutes les autres requêtes (racine `/`) sont transmises au service **Frontend**.
- **Configuration :** `proxy.conf` définit les serveurs amont (upstream) et les règles de routage.
- **EN :** `ENTRYPOINT ["/sbin/tini", "--"]` -> tini est un petit système d'initialisation ("init system"). J'utilise alors tini parce que Node.js n'est pas conçu pour s'exécuter en tant que PID 1 et ne gère pas toujours correctement ces signaux.

### 2. Frontend
- **Dossier :** `src/frontend`
- **Port Interne :** `8080` (Non exposé directement à l'hôte)
- **Technologie :** Nginx (servant du contenu statique) & Angular (référencé dans `package.json`)
- **Rôle :** Sert l'interface utilisateur.
- **Dockerfile :** Construit une image Nginx avec une configuration personnalisée pour servir un `index.html`.

### 3. Backend
- **Dossier :** `src/backend`
- **Port Interne :** `3000` (Non exposé directement à l'hôte)
- **Technologie :** Node.js (v20-alpine), Express
- **Rôle :** Gère les requêtes API.
- **Endpoints :**
  - `GET /health` : Endpoint de vérification de santé (proxifié via `/api/health` actuellement ou `/api/` selon l'implémentation exacte du serveur).

---

## SCHÉMA DE COMMUNICATION

Le diagramme suivant illustre la communication entre les services au sein du réseau Docker.

```mermaid
graph TD;
    User[Navigateur Web / Utilisateur] -->|HTTP Port 80| Proxy[Webserver (Nginx Proxy)];
    
    subgraph Docker Network
        Proxy -->|/ (Racine)| Frontend[Service Frontend :8080];
        Proxy -->|/api/*| Backend[API Backend :3000];
    end
    
    style User fill:#f9f,stroke:#333,stroke-width:2px
    style Proxy fill:#bbf,stroke:#333,stroke-width:2px
    style Frontend fill:#dfd,stroke:#333,stroke-width:2px
    style Backend fill:#ffd,stroke:#333,stroke-width:2px
```

### Description du Flux
1. **L'Utilisateur** effectue une requête vers `http://localhost`.
2. **Le Webserver (Proxy)** reçoit la requête.
3. Si le chemin est `/api/`, la requête est proxifiée vers le conteneur **Backend** sur le port `3000`.
4. Si le chemin est `/` (ou tout autre chose), la requête est proxifiée vers le conteneur **Frontend** sur le port `8080`.
