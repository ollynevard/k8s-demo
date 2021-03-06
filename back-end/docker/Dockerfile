ARG PHP_VERSION=7.4
ARG NGINX_VERSION=1.19.2

#
# PHP: Base ====================================================================
#
FROM php:${PHP_VERSION}-fpm-alpine AS php-base

WORKDIR /app

# Install composer
COPY --from=composer /usr/bin/composer /usr/bin/composer

#
# PHP: Production ==============================================================
#
FROM php-base as php-prod

# Copy PHP config
COPY docker/php.ini /usr/local/etc/php/conf.d/php.ini

# Install application dependencies
COPY app/composer.json app/composer.lock /app/
RUN set -ex; \
    composer install --no-progress --no-dev; \
    rm /usr/bin/composer;

# Copy application
COPY app /app

#
# PHP: Development =============================================================
#
FROM php-base as php-dev

# Install development dependencies
RUN set -ex; \
    apk add --no-cache git ncurses;

# Install Xdebug
ARG XDEBUG_VERSION=2.9.6
RUN set -ex; \
    apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS; \
    mkdir -p /usr/src/php/ext/xdebug; \
    curl -fsSL https://xdebug.org/files/xdebug-${XDEBUG_VERSION}.tgz | \
        tar xvz -C "/usr/src/php/ext/xdebug" --strip 1; \
    docker-php-ext-install xdebug; \
    apk del -f .phpize-deps;

# Install PHIVE
RUN set -ex; \
    apk add --no-cache gnupg; \
    wget -O phive.phar https://phar.io/releases/phive.phar; \
    wget -O phive.phar.asc https://phar.io/releases/phive.phar.asc; \
    gpg --keyserver pool.sks-keyservers.net --recv-keys 9D8A98B29B2D5D79; \
    gpg --verify phive.phar.asc phive.phar; \
    chmod +x phive.phar; \
    mv phive.phar /usr/local/bin/phive; \
    rm phive.phar.asc;

# Install development tools
RUN set -ex; \
    phive install -g --trust-gpg-keys BB5F005D6FFDD89E grumphp@^0.21.0; \
    phive install -g --trust-gpg-keys E82B2FB314E9906E php-cs-fixer@^2.16.4; \
    phive install -g --trust-gpg-keys 4AA394086372C20A phploc@^7.0.1; \
    phive install -g --trust-gpg-keys 8A03EA3B385DBAA1 psalm@^3.14.2; \
    phive install -g --trust-gpg-keys 4F2AB4D11A9A65F7 sensiolabs-de/deptrac@^0.8.2;

# Copy PHP config
COPY docker/php.ini docker/xdebug.ini /usr/local/etc/php/conf.d/

# Install application dependencies
COPY app/composer.json app/composer.lock /app/
RUN composer install --no-progress

# Copy application
COPY app /app

#
# Nginx: Base ==================================================================
#
FROM nginx:${NGINX_VERSION}-alpine AS nginx-base

# Copy Nginx config template
COPY docker/default.conf.template /etc/nginx/templates/

#
# Nginx: Production ============================================================
#
FROM nginx-base as nginx-prod

# Copy public application files
COPY --from=php-prod /app/public /app/public

#
# Nginx: Development ===========================================================
#
FROM nginx-base as nginx-dev

# Copy public application files
COPY --from=php-dev /app/public /app/public
