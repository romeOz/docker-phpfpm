FROM ubuntu:trusty
MAINTAINER romeOz <serggalka@gmail.com>

ENV OS_LOCALE="en_US.UTF-8"
RUN locale-gen ${OS_LOCALE}
ENV LANG=${OS_LOCALE} \
	LANGUAGE=en_US:en \
	LC_ALL=${OS_LOCALE}

ENV PHP_RUN_DIR=/run/php \
	PHP_LOG_DIR=/var/log/php \
	PHP_CONF=/etc/php/7.0/fpm/php-fpm.conf \
	PHP_DATA_DIR=/var/lib/php

RUN	\
	buildDeps='software-properties-common python-software-properties' \
	&& apt-get update \
	&& apt-get install --no-install-recommends -y $buildDeps \
	&& add-apt-repository -y ppa:ondrej/php \
	&& apt-get update \
	&& apt-get install -y curl php7.0-fpm php7.0-cli php7.0-readline php7.0-intl php7.0-json php7.0-curl php7.0-mcrypt php7.0-gd php7.0-pgsql php7.0-mysql \
	&& phpenmod mcrypt \
	# Install composer
	&& curl -sS https://getcomposer.org/installer | php -- --version=1.1.0 --install-dir=/usr/local/bin --filename=composer \
	&& mkdir -p ${PHP_LOG_DIR} ${PHP_RUN_DIR} \
	# Cleaning
	&& apt-get purge -y --auto-remove $buildDeps \
	&& apt-get autoremove -y && apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

COPY ./configs/php-fpm.conf ${PHP_CONF}
COPY ./configs/www.conf /etc/php/7.0/fpm/pool.d/www.conf
COPY ./configs/php.ini /etc/php/7.0/fpm/conf.d/custom.ini

RUN sed -i "s~PHP_RUN_DIR~${PHP_RUN_DIR}~g" ${PHP_CONF} \
	&& sed -i "s~PHP_LOG_DIR~${PHP_LOG_DIR}~g" ${PHP_CONF} \
	&& chown www-data:www-data ${PHP_DATA_DIR} -Rf

WORKDIR /var/www

EXPOSE 9000

# PHP_DATA_DIR store sessions
VOLUME ["${PHP_RUN_DIR}", "${PHP_DATA_DIR}"]
CMD ["/usr/sbin/php-fpm7.0"]