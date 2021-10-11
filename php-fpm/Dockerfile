ARG PHP_VERSION
ARG APP_FULL_PATH

FROM php:${PHP_VERSION}-fpm-alpine

COPY ./wait-for-it.sh /usr/bin/

RUN chmod +x /usr/bin/wait-for-it.sh 
RUN apk --update --no-cache add git
RUN apk --no-cache add curl
RUN apk --no-cache add bash

# GD Extension
RUN apk add --update --no-cache \
        freetype-dev \
        libjpeg-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
    && docker-php-ext-install -j"$(getconf _NPROCESSORS_ONLN)" gd

# xdebug
RUN docker-php-source extract \
    && apk add --no-cache --virtual .phpize-deps-configure $PHPIZE_DEPS \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && apk del .phpize-deps-configure \
    && docker-php-source delete \

# install bcmath extension
RUN docker-php-ext-install bcmath

RUN docker-php-ext-install pdo_mysql

RUN apk add --update npm

RUN apk --no-cache add shadow
RUN usermod -u 1000 www-data
RUN usermod -a -G www-data root
RUN chown -R www-data:www-data /var/www
RUN mkdir -p /var/www/.composer
RUN chown -R www-data:www-data /var/www/.composer

RUN composer create-project

COPY --from=composer /usr/bin/composer /usr/local/bin/composer

WORKDIR /var/www/project
CMD php-fpm
EXPOSE 9000
