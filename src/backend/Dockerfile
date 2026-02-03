# Utilisation de alpine pour gagner en performance
FROM node:20-alpine

# Installation du gestionnaire de process tini
RUN apk add --no-cache tini

WORKDIR /usr/src/app

# Variable d'environnement pour la production
ENV NODE_ENV=production

COPY package*.json ./

# Installation propre des dépendances de production et nettoyage du cache
RUN npm ci --only=production && npm cache clean --force

COPY . .

# Attribution des permissions à l'utilisateur node
RUN chown -R node:node /usr/src/app

# Bascule vers l'utilisateur node pour la sécurité
USER node

EXPOSE 3000


# Utilisation de tini comme init system pour gérer correctement les signaux (PID 1)
# Configuration du point d'entrée avec tini
ENTRYPOINT ["/sbin/tini", "--"]

CMD ["node", "server.js"]
