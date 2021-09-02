ARG PHP_VERSION=8.0.10
# https://hub.docker.com/_/php
FROM php:${PHP_VERSION}-fpm-buster

ARG XDEBUG_VERSION=3.0.4
ARG COMPOSER_VERSION=2.1.6

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    # Using git for development.
    git \
    # libzip-dev: enable PHP zip extension with `docker-php-ext-install zip` command.
    zip unzip libzip-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN pecl install xdebug-${XDEBUG_VERSION} \
    && docker-php-ext-install zip \
    && docker-php-ext-enable xdebug

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php --version=${COMPOSER_VERSION} --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');"
