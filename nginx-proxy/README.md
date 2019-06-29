# Nginx Reverse Proxy

Documentation: \
[Automated Nginx Reverse Proxy for Docker](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/) \
[GitHub: jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)

Free port 80 (e.g.: from NginX)

```sh
sudo service nginx stop
```

## Build and run

```sh
cd ~/github/arcadas-server/nginx-proxy
# Detached by -d
sudo docker-compose up -d
```

Stop:

```sh
sudo docker stop nginx-proxy
```

Shell access whilst the container is running:

```sh
sudo docker exec -it nginx-proxy /bin/bash
```

To monitor the logs of the container in realtime:

```sh
sudo docker logs -f nginx-proxy
```
