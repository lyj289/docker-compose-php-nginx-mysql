FROM php:7.2-fpm \
  && apt-get update && apt-get install -y libpq-dev \
  && docker-php-ext-install pdo pdo_pgsql pgsql mysqli && docker-php-ext-enable pgsql mysqli \
  && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql
