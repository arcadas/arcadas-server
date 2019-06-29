# Transmission

## Config

Check settings.json and docker-compose.yml and update. \
Documentation: https://hub.docker.com/r/linuxserver/transmission/

```sh
# Put settings.json into config folder
mkdir ~/.config/transmission
cp ~/github/transmission/settings.json ~/.config/transmission
```

In settings.json do not overwrite download dirs! You define it in docker-compose.yml.
```json
"download-dir": "/downloads",
"incomplete-dir": "/downloads",
```

## Build and run

```sh
cd ~/github/arcadas-server/transmission
# Detached by -d
sudo docker-compose up -d
```

Stop:

```sh
sudo docker stop transmission
```

Shell access whilst the container is running:

```sh
sudo docker exec -it transmission /bin/bash
```

To monitor the logs of the container in realtime:

```sh
sudo docker logs -f transmission
```

## Web GUI:

```http
# Change the IP
http://192.168.0.21:9091/transmission/web/#/torrents
```
