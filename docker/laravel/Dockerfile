FROM php:8.2.8-fpm-bullseye

ARG NEW_RELIC_AGENT_VERSION
ARG NEW_RELIC_LICENSE_KEY
ARG NEW_RELIC_APPNAME

ENV TZ=Asia/Tokyo \
    COMPOSER_ALLOW_SUPERUSER=1 \
    COMPOSER_HOME=/composer

RUN curl -k -L https://download.newrelic.com/php_agent/archive/${NEW_RELIC_AGENT_VERSION}/newrelic-php5-${NEW_RELIC_AGENT_VERSION}-linux.tar.gz | tar -C /var -zx \
    && export NR_INSTALL_USE_CP_NOT_LN=1 \
    && export NR_INSTALL_SILENT=1 \
    && /var/newrelic-php5-${NEW_RELIC_AGENT_VERSION}-linux/newrelic-install install \
    && rm -rf /var/newrelic-php5-* /var/nrinstall*

RUN find /usr/local/etc -type f -name newrelic.ini \
    -exec sed -i \
    -e "s/REPLACE_WITH_REAL_KEY/${NEW_RELIC_LICENSE_KEY}/" \
    -e "s/newrelic.appname[[:space:]]=[[:space:]].*/newrelic.appname = \"${NEW_RELIC_APPNAME}\"/" \
    -e 's/;newrelic.daemon.loglevel = "info"/newrelic.daemon.loglevel = "verbosedebug"/'  \
    -e 's/;newrelic.loglevel = "info"/newrelic.loglevel = "verbosedebug"/'  \    
    -e '$anewrelic.daemon.address="newrelic-php-daemon:31339"' {} \;

COPY --from=composer:2.5.8 /usr/bin/composer /usr/bin/composer

RUN apt-get update \
    && apt-get -y install --no-install-recommends \
    git \
    unzip \
    libzip-dev \
    libicu-dev \
    libonig-dev \
    && apt-get -y upgrade \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install \
    intl \
    pdo_mysql \
    zip \
    bcmath \
    && composer config -g process-timeout 3600 \
    && composer config -g repos.packagist composer https://packagist.org

# PHP設定をコピー
COPY ./docker/laravel/php.ini /usr/local/etc/php/php.ini

COPY ./sources /data

WORKDIR /data

RUN composer install \
    && chown -R www-data:www-data ./ \
    && chmod -R 777 storage bootstrap/cache \
    && php artisan optimize:clear \
    && php artisan optimize \
    && php artisan cache:clear \
    && php artisan config:clear \
    && php artisan route:clear \
    && php artisan view:clear