FROM romeoz/docker-phpfpm:7.2
MAINTAINER romeOz <serggalka@gmail.com>

ENV PHP_CONF_DIR=/etc/php/7.2

RUN \
    BUILD_DEPS='php7.2-dev wget' \
    && apt-get update \
    && apt-get install -y ${BUILD_DEPS} php-xdebug php-apcu php-memcache php-memcached php-mongodb php7.2-bcmath \
    && echo "apc.enable_cli=1" >> ${PHP_CONF_DIR}/mods-available/apcu.ini \
    && pecl channel-update pecl.php.net \

    # Install couchbase
    && echo "\nInstall couchbase extension\n" \
    && echo "deb http://packages.couchbase.com/ubuntu bionic bionic/main" | tee /etc/apt/sources.list.d/couchbase.list \
    && wget -O- http://packages.couchbase.com/ubuntu/couchbase.key | apt-key add - \
    && apt-get update \
    && apt-get install -y zlib1g-dev libcouchbase2-libevent libcouchbase-dev re2c \
    && pecl install -f pcs \
    && echo "extension = pcs.so" >> ${PHP_CONF_DIR}/mods-available/pcs.ini \
    && ln -s ${PHP_CONF_DIR}/mods-available/pcs.ini ${PHP_CONF_DIR}/fpm/conf.d/pcs.ini \
    && ln -s ${PHP_CONF_DIR}/mods-available/pcs.ini ${PHP_CONF_DIR}/cli/conf.d/pcs.ini \
    && pecl install -f couchbase-2.6.0 \
    && echo "extension = couchbase.so" >> ${PHP_CONF_DIR}/mods-available/couchbase.ini \
    && ln -s ${PHP_CONF_DIR}/mods-available/couchbase.ini ${PHP_CONF_DIR}/fpm/conf.d/couchbase.ini \
    && ln -s ${PHP_CONF_DIR}/mods-available/couchbase.ini ${PHP_CONF_DIR}/cli/conf.d/couchbase.ini \

    # Install Redis
    && echo "\nInstall redis extension\n" \
    && printf "\n" | pecl install -f redis-4.2.0 \
    && echo "extension = redis.so" >> ${PHP_CONF_DIR}/mods-available/redis.ini \
    && ln -s ${PHP_CONF_DIR}/mods-available/redis.ini ${PHP_CONF_DIR}/fpm/conf.d/redis.ini \
    && ln -s ${PHP_CONF_DIR}/mods-available/redis.ini ${PHP_CONF_DIR}/cli/conf.d/redis.ini \

    # Install PHPUnit
    && echo "\nInstall PHPUnit\n" \
    && composer global require "phpunit/phpunit:^7.0.0" \
    && echo -e '\ninclude_path = ".:/usr/share/php:/root/.composer/vendor/phpunit"' >> ${PHP_CONF_DIR}/cli/conf.d/custom.ini \
    && ln -s /root/.composer/vendor/bin/phpunit /usr/bin/phpunit \

    # Cleaning
    && apt-get purge -y --auto-remove ${BUILD_DEPS} \
    && apt-get autoremove -y && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV PATH /root/.composer/vendor/bin:$PATH

COPY xdebug.ini ${PHP_CONF_DIR}/mods-available/xdebug.ini

