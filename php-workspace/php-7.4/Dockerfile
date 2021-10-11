ARG DOCKER_PHP_VERSION=7.4

FROM php:${DOCKER_PHP_VERSION}-fpm

ARG DOCKER_PHP_ENABLE_XDEBUG='on'
ARG TZ='UTC'

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libffi-dev \
        libgcrypt-dev \
        libpng-dev \
        libmcrypt-dev \
        libxslt-dev \
        libzip-dev \
        libxml2-dev \
		    libpq-dev \
        procps \
	      wget \
        nodejs \
        npm \
        unzip

RUN docker-php-ext-configure bcmath --enable-bcmath && \
    docker-php-ext-configure gd \
        --with-freetype=/usr/include/ \
        --with-jpeg=/usr/include/ && \
    docker-php-ext-configure gettext && \
    docker-php-ext-configure intl --enable-intl && \
    docker-php-ext-configure opcache --enable-opcache && \
    docker-php-ext-configure pcntl --enable-pcntl && \
    docker-php-ext-configure soap && \
    docker-php-ext-configure zip --with-zip && \
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
        pdo_mysql \
		    pdo \
		    pdo_pgsql \
	    	pgsql \
        sockets

# install amqp
RUN apt-get update && apt-get install -y librabbitmq-dev
RUN	pecl install amqp-1.10.2
RUN	docker-php-ext-enable amqp

RUN npm config set strict-ssl false

RUN curl -sS http://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# install xdebug
RUN pecl install xdebug && \
	docker-php-ext-enable xdebug && \
	echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
	echo "xdebug.start_with_request=default" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
	echo "xdebug.remote_handler=dbgp" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
	echo "xdebug.idekey=\"PHPSTORM\"" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
	echo "xdebug.client_port=9003" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
	echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

WORKDIR /var/www/
