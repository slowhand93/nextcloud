## nextcloud-docker

## Synopsis

This nextcloud docker file includes php-fpm, nginx+ssl, letsencrypt.
By default it uses sqlite but can be configured to use external MySql or PostgreSQL database.
When installed first time, it generates the self-signed SSL certificate. It can later by replaced by generating a Let's Encrypt certificate using included tools.

## Installation

Install and run MariaDB 10

Random MySQL root password is generated at install time and outputed to messages log.

Database and user for nextcloud is also created.

```
docker run -d --name nextcloud-db \
       -v /mnt2/nextcloud-db:/var/lib/mysql \
       -e MYSQL_RANDOM_ROOT_PASSWORD=yes \
       -e MYSQL_DATABASE=nextcloud \
       -e MYSQL_USER=nextcloud \
       -e MYSQL_PASSWORD=password \
       mariadb:10
```

Get and build nextcloud-docker

```
git clone https://github.com/martmaiste/nextcloud-docker.git
docker build -t nextcloud nextcloud-docker
```

Run nextcloud-docker

DOMAIN and EMAIL are mainly used for generating Let's Encrypt certificate later. Remove if not needed.

Nextcloud needs to be accessible on ports 80 and 443 for generating Let's Encrypt certificates.

```
docker run --name nextcloud -p 80:8888 -p 443:4430 \
       --link nextcloud-db:nextcloud-db \
       -v /mnt2/nextcloud-data:/data \
       -v /mnt2/nextcloud-config:/config \
       -v /mnt2/nextcloud-app:/apps2 \
       -e UID=1000 -e GID=1000 \
       -e UPLOAD_MAX_SIZE=10G \
       -e APC_SHM_SIZE=128M \
       -e OPCACHE_MEM_SIZE=128 \
       -e CRON_PERIOD=15m \
       -e DB_TYPE=mysql \
       -e DB_HOST=nextcloud-db \
       -e DB_USER=nextcloud \
       -e DB_PASSWORD=password \
       -e TZ=Etc/UTC \
       -e DOMAIN=localhost \
       -e EMAIL=hostmaster@localhost \
       -t nextcloud
```

## Credits

[Nextcloud 11 Dockerfile by Wonderfall](https://github.com/Wonderfall/dockerfiles/tree/master/nextcloud/11.0)

[Nginx with SSL and Let's Eencrypt support by ngineered](https://github.com/ngineered/nginx-php-fpm)

[Self-signed certificate generator by paulczar](https://raw.githubusercontent.com/paulczar/omgwtfssl/master/generate-certs)
