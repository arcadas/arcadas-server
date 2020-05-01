# Transmission

Check settings.json and docker-compose.yml and update. \
Documentation: [linuxserver-transmission](https://hub.docker.com/r/linuxserver/transmission/)

In settings.json do not overwrite download dirs! You define it in docker-compose.yml.

```json
"download-dir": "/downloads",
"incomplete-dir": "/downloads",
```

Docker compose: [docker-compose.yml](docker-compose.yml)

Change rpc-whitelist without `403 forbidden` over nginx-proxy!

```json
"rpc-whitelist": "*",
```

Web GUI

```http
# Change the domain name
https://transmission.arcadas.com/transmission/web
```
