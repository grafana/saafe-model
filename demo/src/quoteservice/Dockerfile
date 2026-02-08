# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0


FROM php:8.3-cli@sha256:0ce2b71a35767f95c6e7b02550d0bd2668215a35cd04a83773a540f5c2b610da as base

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions \
  && install-php-extensions \
    opcache \
    pcntl \
    protobuf \
    opentelemetry

WORKDIR /var/www
CMD php public/index.php
USER www-data
EXPOSE ${QUOTE_SERVICE_PORT}

FROM composer:2.7@sha256:cb3483dc851665462a66c59982577dfbbde0ae2059e8b5550c2f49f44b8c333e AS vendor

WORKDIR /tmp/
COPY ./src/quoteservice/composer.json .

RUN composer install \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --no-dev \
    --prefer-dist

FROM base as final
COPY --from=vendor /tmp/vendor/ ./vendor/
COPY ./src/quoteservice/ /var/www

