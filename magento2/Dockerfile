FROM php:8.1-fpm as base

ENV COMPOSER_HOME=/tmp/composer
ENV APCU_VERSION=5.1.18

RUN apt-get update && apt-get install -y --no-install-recommends gnupg \
    netcat \
    sudo \
    libicu-dev \
    libfreetype6-dev \
    libjpeg-dev \
    libpng-dev \
    libsodium-dev \
    libxml2-dev \
    libxslt-dev \
    libzip-dev \
    rsync \
    unzip \
    nginx \
    ;

RUN pecl install apcu-${APCU_VERSION}

RUN docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg
RUN docker-php-ext-install -j "$(nproc)" \
    bcmath \
    gd \
    intl \
    mysqli \
    opcache \
    pdo_mysql \
    sockets \
    soap \
    xsl \
    zip \
    ;

RUN docker-php-ext-enable apcu

RUN echo "memory_limit=1G" >> /usr/local/etc/php/conf.d/zz-memory-limit-php.ini
RUN echo "apc.enable=1" >> /usr/local/etc/php/conf.d/zz-apcu.ini
RUN echo "apc.enable_cli=1" >> /usr/local/etc/php/conf.d/zz-apcu.ini
RUN echo "opcache.memory_consumption=512MB" >> /usr/local/etc/php/conf.d/zz-opcache.conf
RUN echo "opcache.max_accelerated_files=60000" >> /usr/local/etc/php/conf.d/zz-opcache.conf
RUN echo "opcache.consistency_checks=0" >> /usr/local/etc/php/conf.d/zz-opcache.conf
RUN echo "opcache.validate_timestamps=0" >> /usr/local/etc/php/conf.d/zz-opcache.conf
RUN echo "opcache.enable_cli=1" >> /usr/local/etc/php/conf.d/zz-opcache.conf

FROM base as build

RUN curl https://files.magerun.net/n98-magerun2.phar -o /usr/local/bin/magerun \
    && chmod 755 /usr/local/bin/magerun

USER www-data
WORKDIR /var/www/html

ARG COMPOSER_AUTH

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

FROM build as app

ENV MAGE_MODE=production