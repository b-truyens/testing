FROM php:8.1-fpm as php

ENV PHP_OPCACHE_ENABLE=1
ENV PHP_OPCACHE_ENABLE_CLI=0
ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS=1
ENV PHP_OPCACHE_REVALIDATE_FREQ=1

RUN usermod -u 1000 www-data

COPY --chown=www-data:www-data . .

RUN apt-get update -y
RUN apt-get install -y \ 
    build-essential libpng-dev libfreetype6-dev \
    locales zip jpegoptim optipng pngquant gifsicle \
    libjpeg62-turbo-dev vim git curl libzip-dev libgd-dev \ 
    unzip libpq-dev libcurl4-gnutls-dev zlib1g-dev libonig5\
    nginx nano net-tools nmap traceroute iputils-ping mc wget automake libtool

RUN curl -sL https://deb.nodesource.com/setup_20.x | bash -
RUN apt-get install -y nodejs 


RUN mkdir /tmp/oniguruma && wget -c https://github.com/kkos/oniguruma/archive/refs/tags/v6.9.8.tar.gz -O - | tar -xz -C /tmp/oniguruma/
WORKDIR /tmp/oniguruma/oniguruma-6.9.8

RUN cd /tmp/oniguruma/oniguruma-6.9.8/
RUN autoreconf -vfi && ./configure
RUN make && make install
RUN onig-config --cflags --libs --prefix --exec-prefix

RUN docker-php-ext-install pdo pdo_mysql bcmath curl opcache mbstring zip exif pcntl
RUN docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/
RUN docker-php-ext-install gd

# RUN docker-php-ext-enable opcache

WORKDIR /var/www

COPY --chown=www-data:www-data . .

COPY ./docker/php/php.ini /usr/local/etc/php/php.ini
COPY ./docker/php/php-fpm.conf /usr/local/etc/php-fpm.d/www.conf
COPY ./docker/php/opcache.ini /usr/local/etc/php/conf.d/opcache.ini
COPY ./docker/nginx/nginx.conf /etc/nginx/nginx.conf


# FROM node:latest

# RUN curl -sL https://deb.nodesource.com/setup_20.x | bash -
# RUN apt-get install -y nodejs 

COPY --from=composer:2.5.8 /usr/bin/composer /usr/bin/composer

RUN chown -hR www-data:www-data /var/www

WORKDIR /var/www/

RUN cd /var/www/ 


RUN chown -hR www-data:www-data /var/www
RUN chown -hR www-data:www-data /var/www/.*
RUN chown -hR www-data:www-data /var/www/*



RUN chmod -R 755 /var/www/storage
RUN chmod -R 755 /var/www/bootstrap

RUN chmod 777 /var/www/docker/entrypoint.sh

RUN ["chmod", "+x", "docker/entrypoint.sh"]

ENTRYPOINT [ "docker/entrypoint.sh" ]