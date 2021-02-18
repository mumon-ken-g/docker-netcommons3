FROM php:7.4-apache

RUN apt-get update && \
  apt-get install -y unzip libonig-dev && \
  docker-php-ext-install pdo_mysql mysqli && \
  a2enmod rewrite actions

# for imagick
RUN apt-get install -y --no-install-recommends libmagickwand-dev \
  && pecl install imagick \
  && docker-php-ext-enable imagick

RUN mkdir -p /usr/src/netcommons \
  && curl -SL https://www.netcommons.org/cabinets/cabinet_files/download/50/7038d9de4398db3532d626f6ad16394f?frame_id=63 -o /usr/src/netcommons/nc.zip \
  && unzip /usr/src/netcommons/nc.zip -d /usr/src/netcommons \
  && mv /usr/src/netcommons/NetCommons3/* /usr/src/netcommons/NetCommons3/.[^\.]* /var/www/html/ \
  && rm -rf /usr/src/netcommons \
  && chown -R www-data:www-data /var/www/html/*

# for SSL
# COPY ssl.conf /etc/apache2/sites-available/ssl.conf
# RUN a2enmod ssl && a2ensite ssl

