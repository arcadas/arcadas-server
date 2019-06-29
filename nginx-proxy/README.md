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
