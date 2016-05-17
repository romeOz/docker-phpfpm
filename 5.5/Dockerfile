FROM ubuntu:trusty
MAINTAINER romeOz <serggalka@gmail.com>

ENV OS_LOCALE="en_US.UTF-8"
RUN locale-gen ${OS_LOCALE}
ENV LANG=${OS_LOCALE} \
	LANGUAGE=en_US:en \
	LC_ALL=${OS_LOCALE}

ENV PHP_RUN_DIR=/run/php \
	PHP_LOG_DIR=/var/log/php \
	PHP_CONF=/etc/php/5.5/fpm/php-fpm.conf \
	PHP_DATA_DIR=/var/lib/php

RUN	\
	buildDeps='software-properties-common python-software-properties' \
	&& apt-get update \
	&& apt-get install --no-install-recommends -y $buildDeps \
	&& add-apt-repository -y ppa:ondrej/php \
	&& apt-get update \
	&& apt-get install -y curl php5.5-fpm php5.5-cli php5.5-intl php5.5-json php5.5-curl php5.5-mcrypt php5.5-gd php5.5-pgsql php5.5-mysql \
	&& phpenmod mcrypt \
	# Install composer
	&& curl -sS https://getcomposer.org/installer | php -- --version=1.1.0 --install-dir=/usr/local/bin --filename=composer \
	&& mkdir -p ${PHP_LOG_DIR} ${PHP_RUN_DIR} \
	# Cleaning
	&& apt-get purge -y --auto-remove $buildDeps \
	&& apt-get autoremove -y && apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

COPY ./configs/php-fpm.conf ${PHP_CONF}
COPY ./configs/www.conf /etc/php/5.5/fpm/pool.d/www.conf
COPY ./configs/php.ini /etc/php/5.5/fpm/conf.d/custom.ini

RUN sed -i "s~PHP_RUN_DIR~${PHP_RUN_DIR}~g" ${PHP_CONF} \
	&& sed -i "s~PHP_LOG_DIR~${PHP_LOG_DIR}~g" ${PHP_CONF} \
	&& chown www-data:www-data ${PHP_DATA_DIR} -Rf

WORKDIR /var/www

EXPOSE 9000

# PHP_DATA_DIR store sessions
VOLUME ["${PHP_RUN_DIR}", "${PHP_DATA_DIR}"]
CMD ["/usr/sbin/php-fpm5.5"]