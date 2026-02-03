# Utilisation de alpine pour gagner en performance
FROM nginx:alpine

# Copie de la configuration nginx personnalisée
COPY nginx.custom.conf /etc/nginx/conf.d/default.conf

# Copie du fichier index.html vers le répertoire par défaut
COPY index.html /usr/share/nginx/html/index.html

EXPOSE 8080

# Configuration du point d'entrée pour exécuter Nginx en premier plan
ENTRYPOINT ["nginx", "-g", "daemon off;"]
