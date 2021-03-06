#
# PHP Dependencies
#
FROM shippingdocker/php-composer:latest as vendor
WORKDIR /app

COPY htdocs/composer.json composer.json
COPY htdocs/composer.lock composer.lock

RUN composer install \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --prefer-dist

#
# Application
#
FROM php:7.2-apache-stretch


RUN apt-get update && apt-get install -y \
      libicu-dev \
      libpq-dev \
      zlib1g-dev \
      vim curl debconf subversion git apt-transport-https apt-utils \
      build-essential locales acl mailutils wget zip unzip \
      gnupg gnupg1 gnupg2
    && rm -r /var/lib/apt/lists/* \
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-install \
      intl \
      mbstring \
      pcntl \
      pdo_mysql \
      pdo_pgsql \
      pgsql \
      zip \
      opcache

COPY docker/application/symfony.conf /etc/apache2/sites-available/symfony.conf
RUN a2dissite 000-default.conf && a2ensite symfony.conf && a2enmod rewrite

COPY --from=vendor /app/vendor/ /var/www/html/vendor/

#COPY --chown=www-data:www-data htdocs /var/www/html
COPY htdocs /var/www/html

RUN echo 'alias ll="ls -la"' >> /root/.bashrc
