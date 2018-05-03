FROM romeoz/docker-phpfpm:5.5
MAINTAINER romeOz <serggalka@gmail.com>

ENV PHP_CONF_DIR=/etc/php/5.5

RUN \
    BUILD_DEPS='php5.5-dev wget' \
    && apt-get update \
    && apt-get install -y ${BUILD_DEPS} \
    && echo "deb http://packages.couchbase.com/ubuntu trusty trusty/main" | tee -a /etc/apt/sources.list.d/couchbase.list \
    && wget -O- http://packages.couchbase.com/ubuntu/couchbase.key | apt-key add - \
    && apt-get update \
    && apt-get install -y libcouchbase2-libevent libcouchbase-dev php-xdebug php-redis php-memcache php-memcached php-mongo php5.5-bcmath \
    && pecl channel-update pecl.php.net \
    # Install couchbase
    && echo "\nInstall couchbase extension\n" \
    && apt-get install -y re2c \
    && pecl install -f pcs \
    && echo "extension = pcs.so" >> ${PHP_CONF_DIR}/fpm/conf.d/pcs.ini \
    && echo "extension = pcs.so" >> ${PHP_CONF_DIR}/cli/conf.d/pcs.ini \
    && pecl install -f couchbase-2.2.3 \
    && echo "extension = couchbase.so" >> ${PHP_CONF_DIR}/fpm/conf.d/couchbase.ini \
    && echo "extension = couchbase.so" >> ${PHP_CONF_DIR}/cli/conf.d/couchbase.ini \
    # Install APC
    && printf "\n" | pecl install -f apcu-4.0.11 \
    && echo "extension = apcu.so" >> ${PHP_CONF_DIR}/fpm/conf.d/apcu.ini \
    && echo "extension = apcu.so\napc.enable_cli=1" >> ${PHP_CONF_DIR}/cli/conf.d/apcu.ini \
    && composer global require "phpunit/phpunit:^4.7.0" \
    && echo -e '\ninclude_path = ".:/usr/share/php:/root/.composer/vendor/phpunit"' >> ${PHP_CONF_DIR}/cli/conf.d/custom.ini \
    && ln -s /root/.composer/vendor/bin/phpunit /usr/bin/phpunit \
    # Cleaning
    && apt-get purge -y --auto-remove ${BUILD_DEPS} \
    && apt-get autoremove -y && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV PATH /root/.composer/vendor/bin:$PATH

COPY xdebug.ini ${PHP_CONF_DIR}/mods-available/xdebug.ini

