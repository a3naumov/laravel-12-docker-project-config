FROM php:8.3.16-fpm-alpine3.21

ARG XDEBUG_VERSION=3.4.0
ARG COMPOSER_VERSION=2.8.4

# Install extensions
RUN apk add --no-cache \
    linux-headers \
    git \
    curl \
    zip \
    $PHPIZE_DEPS \
    libpq-dev  \
&& docker-php-ext-install \
    pdo_mysql \
    opcache \
;

# Install apcu
RUN pecl install apcu && docker-php-ext-enable apcu;

# Install xdebug
RUN pecl install xdebug-${XDEBUG_VERSION} && docker-php-ext-enable xdebug;

# Install Composer
RUN  php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
     php composer-setup.php --version=${COMPOSER_VERSION} && \
     php -r "unlink('composer-setup.php');" && \
     mv composer.phar /usr/local/bin/composer \
;

# Copy PHP configurations
ARG PHP_INI_FILENAME
ADD ./conf/${PHP_INI_FILENAME} /usr/local/etc/php/conf.d/php.ini

ADD ./conf/ext/php-ext-xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Create user
ARG USER_NAME
ARG USER_ID
ARG GROUP_ID
RUN addgroup -g ${GROUP_ID} ${USER_NAME} && \
    adduser -D -u ${USER_ID} -G ${USER_NAME} ${USER_NAME}
USER ${USER_NAME}

WORKDIR /var/www/src

CMD ["php-fpm"]