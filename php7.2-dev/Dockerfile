FROM php:7.2
MAINTAINER Sangrak Choi <kars@op.gg>

# 필수 설치
# Install "curl", "libmemcached-dev", "libpq-dev", "libjpeg-dev",
#         "libpng12-dev", "libfreetype6-dev", "libssl-dev", "libmcrypt-dev",
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
 && curl \
 && libcurl3 libcurl3-dev \
 && libmemcached-dev \
 && libz-dev \
 && libpq-dev \
 && libjpeg-dev \
 && libpng-dev \
 && libfreetype6-dev \
 && libssl-dev \
 && libmcrypt-dev

# Install the PHP essential extention
RUN docker-php-ext-install -j$(nproc) pdo_mysql \
 && docker-php-ext-install -j$(nproc) bcmath \
 && docker-php-ext-install -j$(nproc) opcache \
 && docker-php-ext-install -j$(nproc) zip

# Install the PHP gd library
RUN docker-php-ext-configure gd \
    --enable-gd-native-ttf \
    --with-jpeg-dir=/usr/lib \
    --with-freetype-dir=/usr/include/freetype2 && \
    docker-php-ext-install gd

# Install pecl modules
RUN pecl install mongodb-1.3.4 \
 && docker-php-ext-enable mongodb \
 && pecl install apcu && pecl install apcu_bc-1.0.3 \
 && docker-php-ext-enable apcu

# Install php-igbinary
RUN mkdir -p /usr/src/php/ext/

RUN cd /usr/src/php/ext \
 && curl -fsSL https://github.com/igbinary/igbinary/archive/master.tar.gz -o igbinary.tar.gz \
 && mkdir -p igbinary \
 && tar -xf igbinary.tar.gz -C igbinary --strip-components=1 \
 && rm igbinary.tar.gz \
 && docker-php-ext-install igbinary

# Install php-redis
RUN cd /usr/src/php/ext \
 && curl -fsSL https://github.com/phpredis/phpredis/archive/3.1.5.tar.gz -o redis.tar.gz \
 && mkdir -p redis \
 && tar -xf redis.tar.gz -C redis --strip-components=1 \
 && rm redis.tar.gz \
 && docker-php-ext-configure redis --enable-redis-igbinary \
 && docker-php-ext-install redis

# Install xdebug
RUN pecl install xdebug-2.6.0 \
 && docker-php-ext-enable xdebug

# Install Node
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
    apt-get install nodejs
RUN npm i -g yarn

# Add custom config
ADD ./conf.d/*.ini /usr/local/etc/php/conf.d/
ADD ./php.ini /usr/local/etc/php/php.ini

# 편의기능 추가 (Composer, art alias)
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN echo '#!/usr/bin/env bash\n' > '/usr/local/bin/art' \
 && echo 'php artisan $@' >> '/usr/local/bin/art' \
 && chmod +x /usr/local/bin/art \
 && composer global require hirak/prestissimo

WORKDIR /var/www

CMD php ./artisan serve --port=80 --host=0.0.0.0
EXPOSE 80
HEALTHCHECK --interval=1m CMD curl -f http://localhost/ || exit 1