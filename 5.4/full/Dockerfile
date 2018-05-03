FROM romeoz/docker-phpfpm:5.4
MAINTAINER romeOz <serggalka@gmail.com>

ENV PHP_CONF_DIR=/etc/php5/

RUN \
    BUILD_DEPS='php5-dev wget make libtool locate pkg-config' \
    && apt-get update \
    && apt-get install -y ${BUILD_DEPS} \
    && echo "deb http://packages.couchbase.com/ubuntu precise precise/main" | tee -a /etc/apt/sources.list.d/couchbase.list \
    && wget -O- http://packages.couchbase.com/ubuntu/couchbase.key | apt-key add - \
    && apt-get update \
    && apt-get install -y libcouchbase2-libevent libcouchbase-dev libsasl2-dev libmemcached-dev php5-memcache \
    && pecl channel-update pecl.php.net \
    # Install xdebug
    && echo -e "\nInstall xdebug extension\n" \
    && pecl install -f xdebug-2.4.0 \
    && PATH_XDEBUG=$(updatedb && locate xdebug.so) \
    && echo "zend_extension = ${PATH_XDEBUG}" >> ${PHP_CONF_DIR}/fpm/conf.d/xdebug.ini \
    # Install memcached
    && echo -e "\nInstall memcached extension\n" \
    && printf "no --disable-memcached-sasl" | pecl install memcached \
    && echo "extension = memcached.so" > ${PHP_CONF_DIR}/fpm/conf.d/memcached.ini \
    # Install mongo
    && echo "\nInstall mongo extension\n" \
    && printf "\n" | pecl install -f mongo \
    && echo "extension = mongo.so" > ${PHP_CONF_DIR}/fpm/conf.d/mongo.ini \
    && echo "extension = mongo.so" > ${PHP_CONF_DIR}/cli/conf.d/mongo.ini \
    # Install redis
    && echo "\nInstall redis extension\n" \
    && printf "\n" | pecl install -f redis-2.2.8 \
    && echo "extension = redis.so" > ${PHP_CONF_DIR}/fpm/conf.d/redis.ini \
    && echo "extension = redis.so" > ${PHP_CONF_DIR}/cli/conf.d/redis.ini \
    # Install couchbase
    && echo "\nInstall couchbase extension\n" \
    && apt-get install -y re2c \
    && pecl install -f pcs \
    && echo "extension = pcs.so" >> ${PHP_CONF_DIR}/fpm/conf.d/pcs.ini \
    && echo "extension = pcs.so" >> ${PHP_CONF_DIR}/cli/conf.d/pcs.ini \
    && pecl install -f couchbase-2.2.3 \
    && echo "extension = couchbase.so" > ${PHP_CONF_DIR}/fpm/conf.d/couchbase.ini \
    && echo "extension = couchbase.so" > ${PHP_CONF_DIR}/cli/conf.d/couchbase.ini \
    && composer global require "phpunit/phpunit:^4.7.0" \
    && echo -e '\ninclude_path = ".:/usr/share/php:/root/.composer/vendor/phpunit"' >> ${PHP_CONF_DIR}/cli/conf.d/custom.ini \
    && ln -s /root/.composer/vendor/bin/phpunit /usr/bin/phpunit \
    # Cleaning
    && apt-get purge -y --auto-remove ${BUILD_DEPS} \
    && apt-get autoremove -y && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV PATH /root/.composer/vendor/bin:$PATH

COPY xdebug.ini ${PHP_CONF_DIR}/mods-available/xdebug.ini

