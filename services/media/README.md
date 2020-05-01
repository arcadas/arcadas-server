# Media

Github: [arcadas/dts2ac3-webgui](https://github.com/arcadas/dts2ac3-webgui) \
Docker compose: [docker-compose.yml](docker-compose.yml) \
Nginx config: [default.conf](media/nginx/default.conf) \
PHP-FPM with MKV tools: [Dockerfile](media/php/Dockerfile)

Build image:

```sh
cd media/php
docker build -t php-fpm-mkv .
```

Set write permission

```sh
chmod a+w -R /media/nas/movies
```

TODO - set proper user roles instead global write permission!

Blog: [Dockerised Nginx with PHP](http://geekyplatypus.com/dockerise-your-php-application-with-nginx-and-php7-fpm/)
