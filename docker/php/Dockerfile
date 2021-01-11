# https://hub.docker.com/_/php
FROM php:8.0.1-fpm-alpine3.12

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

COPY --from=composer:2.0.8 /usr/bin/composer /workspace/bin/composer
