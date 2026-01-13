FROM nginx:alpine

COPY nginx.custom.conf /etc/nginx/conf.d/default.conf

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 8080

ENTRYPOINT ["nginx", "-g", "daemon off;"]
