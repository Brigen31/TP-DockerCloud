FROM nginx:alpine

RUN apk add --no-cache curl openssl

RUN rm /etc/nginx/conf.d/default.conf

COPY proxy.conf /etc/nginx/conf.d/default.conf

# Limiter les permissions
RUN chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid

USER nginx

EXPOSE 80

ENTRYPOINT ["nginx", "-g", "daemon off;"]
