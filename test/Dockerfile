FROM php:7-apache AS base

WORKDIR /code

FROM base AS extensions

RUN apt update && apt install --yes zlib1g-dev
RUN docker-php-ext-install pdo_mysql zip

FROM base AS composer

ENV COMPOSER_HOME ./.composer
ENV DEBIAN_FRONTEND noninteractive

COPY --from=extensions \
    /usr/lib/x86_64-linux-gnu/libz.so \
    /usr/lib/x86_64-linux-gnu/libz.so

COPY --from=extensions \
    /usr/local/lib/php/extensions/no-debug-non-zts-20170718/pdo_mysql.so \
    /usr/local/lib/php/extensions/no-debug-non-zts-20170718/pdo_mysql.so

COPY --from=extensions \
    /usr/local/lib/php/extensions/no-debug-non-zts-20170718/zip.so \
    /usr/local/lib/php/extensions/no-debug-non-zts-20170718/zip.so

RUN apt update \
    && apt install --yes git unzip \
    && rm -rf /var/lib/apt

COPY --from=composer:1.7.2 /usr/bin/composer /usr/bin/composer

FROM composer AS deps

COPY composer.json /code/composer.json

RUN composer install --no-dev

FROM deps AS ci

RUN composer install

FROM ci as unit-test

RUN phpunit

FROM ci AS integration-tests

RUN phantomjs

FROM deps AS production

COPY ./opt/apache2/virtualhost.conf /etc/apache2/sites-enabled/000-default.conf
COPY . /code

ENTRYPOINT [ "apache2-foreground" ]