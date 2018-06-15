FROM ubuntu:precise
MAINTAINER romeOz <serggalka@gmail.com>

ENV OS_LOCALE="en_US.UTF-8" \
    DEBIAN_FRONTEND=noninteractive
RUN locale-gen ${OS_LOCALE}
ENV LANG=${OS_LOCALE} \
    LANGUAGE=${OS_LOCALE} \
    LC_ALL=${OS_LOCALE}

ENV PHP_RUN_DIR=/run/php \
    PHP_LOG_DIR=/var/log/php \
    PHP_CONF_DIR=/etc/php5 \
    PHP_DATA_DIR=/var/lib/php5

RUN \
    BUILD_DEPS='software-properties-common python-software-properties' \
    && apt-get update \
    # Install common libraries
    && apt-get install --no-install-recommends -y $BUILD_DEPS \
    # Note: PHP 5.3.29 Trusty ppa:hentenaar/php
    && add-apt-repository ppa:rip84/php5 \
    && apt-get update \
    # Install PHP libraries
    && apt-get install -y curl php5-fpm php5-cli php-apc php5-intl php5-json php5-curl php5-mcrypt php5-gd php5-pgsql php5-mysql php-pear \
    && echo "apc.enable_cli=1" >> ${PHP_CONF_DIR}/cli/conf.d/apc.ini \
    # Install composer
    && curl -sS https://getcomposer.org/installer | php -- --version=1.4.3 --install-dir=/usr/local/bin --filename=composer \
    && mkdir -p ${PHP_LOG_DIR} ${PHP_RUN_DIR} \
    # Cleaning
    && apt-get purge -y --auto-remove $BUILD_DEPS \
    && apt-get autoremove -y && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY ./configs/php-fpm.conf ${PHP_CONF_DIR}/fpm/php-fpm.conf
COPY ./configs/www.conf ${PHP_CONF_DIR}/fpm/pool.d/www.conf
COPY ./configs/php.ini ${PHP_CONF_DIR}/fpm/conf.d/custom.ini

RUN sed -i "s~PHP_RUN_DIR~${PHP_RUN_DIR}~g" ${PHP_CONF_DIR}/fpm/php-fpm.conf \
    && sed -i "s~PHP_LOG_DIR~${PHP_LOG_DIR}~g" ${PHP_CONF_DIR}/fpm/php-fpm.conf \
    && chown www-data:www-data ${PHP_DATA_DIR} -Rf

WORKDIR /var/www

EXPOSE 9000

# PHP_DATA_DIR store sessions
VOLUME ["${PHP_RUN_DIR}", "${PHP_DATA_DIR}"]
CMD ["/usr/sbin/php5-fpm"]