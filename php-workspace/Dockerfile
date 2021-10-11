ARG DOCKER_PHP_VERSION=7.3

FROM php:${DOCKER_PHP_VERSION}-fpm-alpine

ARG DOCKER_PHP_ENABLE_XDEBUG='off'
ARG TZ='UTC'

# https://wiki.alpinelinux.org/wiki/Setting_the_timezone
RUN echo "${TZ}" && apk --update add tzdata && \
    cp /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    apk del tzdata

RUN apk add --update --no-cache icu-libs \
        libintl \
        build-base \
        zlib-dev \
        cyrus-sasl-dev \
        libgsasl-dev \
        oniguruma-dev \
        procps \
        imagemagick \
        patch \
        bash \
        htop \
        acl \
        apk-cron \
        augeas-dev \
        autoconf \
        curl \
        ca-certificates \
        dialog \
        freetype-dev \
        gomplate \
        git \
        gcc \
        gettext-dev \
        icu-dev \
        libcurl \
        libffi-dev \
        libgcrypt-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        libmcrypt-dev \
        libressl-dev \
        libxslt-dev \
        libzip-dev \
        linux-headers \
        libxml2-dev \
        ldb-dev \
        make \
        musl-dev \
        mysql-client \
        openssh-client \
        pcre-dev \
        ssmtp \
        sqlite-dev \
        supervisor \
        su-exec \
        wget \
        nodejs \
        npm \
        unzip

#  Install php extensions
RUN php -m && \
    docker-php-ext-configure bcmath --enable-bcmath && \
    docker-php-ext-configure gd \
      --with-freetype-dir=/usr/include/ \
      --with-jpeg-dir=/usr/include/ \
      --with-png-dir=/usr/include/ && \
    docker-php-ext-configure gettext && \
    docker-php-ext-configure intl --enable-intl && \
    docker-php-ext-configure opcache --enable-opcache && \
    docker-php-ext-configure pcntl --enable-pcntl && \
    docker-php-ext-configure soap && \
    docker-php-ext-configure zip --enable-zip --with-libzip && \
    docker-php-ext-install exif \
        mysqli \
        opcache \
        xsl \
        bcmath \
        gd \
        gettext \
        intl \
        opcache \
        pcntl \
        soap \
        zip \
        calendar \
        pdo_mysql
RUN printf "\n" | curl 'http://pecl.php.net/get/redis-5.3.1.tgz' -o redis-5.3.1.tgz \
    && pecl install redis-5.3.1.tgz \
    &&  rm -rf redis-5.3.1.tgz \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis
RUN apk add --update --no-cache --virtual .docker-php-mongodb-dependencies heimdal-dev
RUN	printf "\n" | curl 'http://pecl.php.net/get/mongodb-1.8.0.tgz' -o mongodb-1.8.0.tgz \
    && pecl install mongodb-1.8.0.tgz \
    &&  rm -rf mongodb-1.8.0.tgz \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable mongodb
RUN apk del .docker-php-mongodb-dependencies
RUN apk add --update --no-cache libpq && \
    # Build dependancies for PostgreSQL \
    apk add --update --no-cache --virtual .docker-php-postgresql-dependencies \
        postgresql-client \
        postgresql-dev && \
    docker-php-ext-configure pdo_pgsql && \
    docker-php-ext-configure pgsql && \
    docker-php-ext-install pdo_pgsql \
        pgsql && \
    apk del .docker-php-postgresql-dependencies

# Enable Xdebug
RUN if [ "${DOCKER_PHP_ENABLE_XDEBUG}" == "on" ]; then \
	  yes | printf "\n" | curl 'http://pecl.php.net/get/xdebug-2.9.8.tgz' -o xdebug-2.9.8.tgz \
      && pecl install xdebug-2.9.8.tgz \
      &&  rm -rf xdebug-2.9.8.tgz \
      &&  rm -rf /tmp/pear \
      echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini && \
      echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini && \
      echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini && \
      php -m; \
    else \
      echo "Skip xdebug support"; \
    fi

# Clean
RUN rm -rf /var/cache/apk/* && docker-php-source delete

RUN npm config set strict-ssl false

USER root

RUN curl -sS http://getcomposer.org/installer | php -- --version=1.10.19 --install-dir=/usr/local/bin --filename=composer
#RUN mv composer.phar /usr/local/bin/composer

USER www-data:www-data

WORKDIR /var/www/
