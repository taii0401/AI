FROM php:8.4-fpm-bookworm

ARG APP_USER=app
ARG APP_UID=1000
ARG APP_GID=1000

ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_HOME=/tmp/composer

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        curl \
        unzip \
        zip \
        libicu-dev \
        libzip-dev \
        libonig-dev \
        libpng-dev \
        libjpeg62-turbo-dev \
        libfreetype6-dev \
    && docker-php-ext-configure gd \
        --with-freetype \
        --with-jpeg \
    && docker-php-ext-install -j"$(nproc)" \
        bcmath \
        exif \
        gd \
        intl \
        mbstring \
        opcache \
        pcntl \
        pdo_mysql \
        zip \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY --from=composer:2 /usr/bin/composer /usr/local/bin/composer

RUN groupadd --gid "${APP_GID}" "${APP_USER}" \
    && useradd \
        --uid "${APP_UID}" \
        --gid "${APP_GID}" \
        --create-home \
        --shell /bin/bash \
        "${APP_USER}"

WORKDIR /var/www/html

COPY docker/php/php.ini /usr/local/etc/php/conf.d/99-app.ini

USER ${APP_USER}

CMD ["php-fpm"]