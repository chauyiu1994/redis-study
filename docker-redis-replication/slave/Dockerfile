FROM redis:alpine

EXPOSE 6379
COPY slave.conf /etc/redis/slave.conf
ENTRYPOINT redis-server /etc/redis/slave.conf
