FROM php:7.1-fpm

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
    libpng12-dev \
    libfreetype6-dev \
    libssl-dev \
    libmcrypt-dev

# Install the PHP mcrypt extention
RUN docker-php-ext-install -j$(nproc) mcrypt
RUN docker-php-ext-install -j$(nproc) pdo_mysql
RUN docker-php-ext-install -j$(nproc) iconv
RUN docker-php-ext-install -j$(nproc) bcmath
RUN docker-php-ext-install -j$(nproc) opcache
RUN docker-php-ext-install -j$(nproc) zip

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

# Install Node
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
    apt-get install nodejs
RUN npm i -g yarn

# Install New Relic
RUN apt-get update
RUN apt-get -yqq install wget
RUN apt-get -yqq install python-setuptools
RUN easy_install pip
RUN mkdir -p /opt/newrelic
WORKDIR /opt/newrelic
RUN wget -r -nd --no-parent -Alinux.tar.gz \
    http://download.newrelic.com/php_agent/release/ >/dev/null 2>&1 \
    && tar -xzf newrelic-php*.tar.gz --strip=1
ENV NR_INSTALL_SILENT true
RUN bash newrelic-install install
WORKDIR /
RUN pip install newrelic-plugin-agent
RUN mkdir -p /var/log/newrelic
RUN mkdir -p /var/run/newrelic

# Add custom config
ADD ./conf.d/*.ini /usr/local/etc/php/conf.d/
ADD ./php.ini /usr/local/etc/php/php.ini
ADD ./x-fpm.pool.conf /usr/local/etc/php-fpm.d/

# 편의기능 추가 (Composer, art alias)
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN echo '#!/usr/bin/env bash\n' > '/usr/local/bin/art' \
&&  echo 'php artisan $@' >> '/usr/local/bin/art' \
&&  chmod +x /usr/local/bin/art \
&&  composer global require hirak/prestissimo

WORKDIR /var/www
