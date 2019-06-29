# Transmission

## Config

Check settings.json and docker-compose.yml and update. \
Documentation: [linuxserver-transmission](https://hub.docker.com/r/linuxserver/transmission/)

In settings.json do not overwrite download dirs! You define it in docker-compose.yml.

```json
"download-dir": "/downloads",
"incomplete-dir": "/downloads",
```

Docker compose: [nginx-proxy/docker-compose.yml](nginx-proxy/docker-compose.yml)

TODO - Change rpc-whitelist without `403 forbidden` over nginx-proxy!

```json
"rpc-whitelist": "*",
```

## Web GUI

```http
# Change the IP
http://192.168.0.21:9091/transmission/web/#/torrents
```
