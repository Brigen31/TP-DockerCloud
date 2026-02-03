# Utilisation de alpine pour gagner en performance
FROM nginx:alpine

# Installation des dépendances système
RUN apk add --no-cache curl openssl

# Suppression de la configuration par défaut
RUN rm /etc/nginx/conf.d/default.conf

# Copie du fichier de configuration du proxy
COPY proxy.conf /etc/nginx/conf.d/default.conf

# Attribution des permissions à l'utilisateur nginx
RUN chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid

# Bascule vers l'utilisateur nginx pour la sécurité
USER nginx

EXPOSE 80

# Démarrage de nginx en premier plan
ENTRYPOINT ["nginx", "-g", "daemon off;"]
