# docker-cert-bot

`cert-bot.yml` contains a docker-compose config that acts as a reverse proxy for other deployed containers,
so that those containers are available under custom HTTPS domain using Let's Encrypt.

## Requirements:

1. Create an external network

```shell
docker network create nginx-proxy
```

2. Start cert bot.

```shell
docker-compose -f cert-bot.yml up -d
```

3. Add required environment variables to your container definition inside your docker-compose. 
You can find an example definition in `example.docker-compose.yml`.

```yml
version: "3.6"

services:
  web-backend:
    restart: always
    build:
      context: web-backend
    ports:
      - "4321:8080"
    environment:
      VIRTUAL_HOST: api.example.com  ### Add your domain here
      VIRTUAL_PORT: 4321 ### Port by which this container is exposed to the world
      LETSENCRYPT_HOST: api.example.com ### Add your domain here
      LETSENCRYPT_EMAIL: admin@admin.com ### Must be defined, but is not validated
    networks:
      - nginx-proxy  ### REMEMBER to be in the network of your cert-bot.

networks:
  nginx-proxy:
    external:
      name: nginx-proxy
```
