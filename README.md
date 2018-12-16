Docker-compose Base Linux、Nginx(Openresty)、MySQL5.7、PHP7(PHP-FPM)

## 因为相比`nginx:latest`，`nginx:alpine`有几点优势：
* 用的是最新版nginx镜像，功能与`nginx:latest`一模一样
* alpine 镜像用的是[Alpine Linux](https://alpinelinux.org/)内核，比ubuntu内核要小很多。
* `nginx:alpine` 默认支持http2。

## HELP
* [Dockerise your PHP application with Nginx and PHP7-FPM](http://geekyplatypus.com/dockerise-your-php-application-with-nginx-and-php7-fpm/)
* [docker-openresty](https://github.com/openresty/docker-openresty)



ERROR: for nginx  Cannot start service nginx: OCI runtime create failed: container_linux.go:348: