ARG PHP_VERSION=8.0.10
ARG COMPOSER_VERSION=2.1.14

# https://hub.docker.com/_/composer
FROM composer:${COMPOSER_VERSION} as composer-provider

# https://hub.docker.com/_/php
FROM php:${PHP_VERSION}-fpm-buster

# Install Composer.
COPY --from=composer-provider /usr/bin/composer /usr/bin/composer

ARG XDEBUG_VERSION=3.0.4

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
