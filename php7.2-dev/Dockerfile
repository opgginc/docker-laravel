FROM php:7.2
MAINTAINER Sangrak Choi <kars@op.gg>

# 필수 설치
# Install "curl", "libmemcached-dev", "libpq-dev", "libjpeg-dev",
#         "libpng12-dev", "libfreetype6-dev", "libssl-dev", "libmcrypt-dev",
RUN apt-get update \
&&  apt-get install -y --no-install-recommends \
    curl \
    libcurl3 libcurl3-dev \
    libmemcached-dev \
    libz-dev \
    libpq-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libssl-dev \
    libmcrypt-dev \
    libicu-dev \
    libmagickwand-dev \
    gnupg \
    libfcgi-bin \
    wget unzip python-setuptools cron git \
 && apt-get autoremove -y \
 && apt-get clean all \
 && rm -rf /var/lib/apt/lists/*

# Install the PHP essential extention
RUN docker-php-ext-configure intl \
 && docker-php-ext-install -j$(nproc) pdo_mysql bcmath opcache zip intl \
 && docker-php-ext-enable intl \
# Install the PHP gd library
 && docker-php-ext-configure gd \
    --enable-gd-native-ttf \
    --with-jpeg-dir=/usr/lib \
    --with-freetype-dir=/usr/include/freetype2 \
 && docker-php-ext-install gd \
 && pecl install imagick \
 && docker-php-ext-enable imagick \
# Install pecl modules
 && pecl install mongodb-1.3.4 \
 && docker-php-ext-enable mongodb \
 && pecl install apcu && pecl install apcu_bc-1.0.3 \
 && docker-php-ext-enable apcu \
 && pecl install -o -f redis \
 && rm -rf /tmp/pear \
 && docker-php-ext-enable redis \
# Install php-igbinary
 && mkdir -p /usr/src/php/ext/ \
 && cd /usr/src/php/ext \
 && curl -fsSL https://github.com/igbinary/igbinary/archive/master.tar.gz -o igbinary.tar.gz \
 && mkdir -p igbinary \
 && tar -xf igbinary.tar.gz -C igbinary --strip-components=1 \
 && rm igbinary.tar.gz \
 && docker-php-ext-install igbinary \
 && rm -rf igbinary \
# Install php-redis
 && cd /usr/src/php/ext \
 && curl -fsSL https://github.com/phpredis/phpredis/archive/3.1.5.tar.gz -o redis.tar.gz \
 && mkdir -p redis \
 && tar -xf redis.tar.gz -C redis --strip-components=1 \
 && rm redis.tar.gz \
 && docker-php-ext-configure redis --enable-redis-igbinary \
 && docker-php-ext-install redis \
 && rm -rf redis \
# Delete php src
 && rm -rf /usr/src/php

# Install xdebug
RUN pecl install xdebug-2.6.0 \
 && docker-php-ext-enable xdebug

# Install Node
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash - \
 && apt-get install -y nodejs
RUN npm i -g yarn

# Add custom config
ADD php/conf.d /usr/local/etc/php/conf.d/
ADD php/php.ini /usr/local/etc/php/php.ini

# 편의기능 추가 (Composer, art alias)
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN echo '#!/usr/bin/env bash\n' > '/usr/local/bin/art' \
 && echo 'php artisan $@' >> '/usr/local/bin/art' \
 && chmod +x /usr/local/bin/art \
 && composer global require hirak/prestissimo

# SUPER VISORD / CRON
RUN wget https://github.com/Supervisor/supervisor/archive/master.zip -P /tmp/ \
 && unzip /tmp/master.zip -d /tmp \
 && rm /tmp/master.zip \
 && cd /tmp/supervisor-master/ \
 && python /tmp/supervisor-master/setup.py install \
 && rm -rf /tmp/supervisor-master/

RUN mkdir -p /etc/supervisor/conf.d/ \
 && mkdir -p /var/log/supervisor/
ADD supervisor/supervisord.conf /etc/supervisor/supervisord.conf
ADD supervisor/conf.d /etc/supervisor/conf.d/
ADD ./cron /etc/cron.d/php-cron

# Script
ADD entrypoint.sh /usr/bin/entrypoint.sh
RUN chmod +x /usr/bin/entrypoint.sh

WORKDIR /var/www

CMD ["entrypoint.sh"]
EXPOSE 80

HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD curl -f http://127.0.0.1/ || exit 1