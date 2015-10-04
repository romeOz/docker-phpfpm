FROM ubuntu:precise
MAINTAINER romeOz <serggalka@gmail.com>

ENV OS_LOCALE="en_US.UTF-8"
RUN locale-gen ${OS_LOCALE}
ENV LANG=${OS_LOCALE} \
	LANGUAGE=en_US:en \
	LC_ALL=${OS_LOCALE}

ENV PHP_RUNDIR=/run/php \
	PHP_LOGDIR=/var/log/php \
	PHP_CONF=/etc/php5/fpm/php-fpm.conf \
	PHP_DATADIR=/var/lib/php5

RUN	\
	apt-get update \
	&& apt-get install --no-install-recommends -y sudo \
	&& apt-get install -y curl php5-fpm php5-cli php-apc php5-intl php5-json php5-curl php5-mcrypt php5-gd php5-pgsql php5-mysql php-pear \
	&& echo "apc.enable_cli=1" >> /etc/php5/cli/conf.d/apc.ini \
	&& mkdir -p ${PHP_LOGDIR} ${PHP_RUNDIR} \
	# Cleaning
	&& apt-get autoremove -y && apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

COPY ./configs/php-fpm.conf ${PHP_CONF}
COPY ./configs/www.conf /etc/php5/fpm/pool.d/www.conf
COPY ./configs/php.ini /etc/php5/fpm/conf.d/custom.ini

RUN sed -i "s~PHP_RUNDIR~${PHP_RUNDIR}~g" ${PHP_CONF} \
	&& sed -i "s~PHP_LOGDIR~${PHP_LOGDIR}~g" ${PHP_CONF} \
	&& chown www-data:www-data ${PHP_DATADIR} -Rf

WORKDIR /var/www

EXPOSE 9000

# PHP_DATADIR store sessions
VOLUME ["${PHP_RUNDIR}", "${PHP_DATADIR}"]
CMD ["/usr/sbin/php5-fpm"]