FROM webdevops/php-apache:7.2

# set env
ENV WEB_DOCUMENT_INDEX index.php

# add pem file to system
RUN  wget http://curl.haxx.se/ca/cacert.pem -P /usr/local/share/ca-certificates/

# copy php ini
COPY php.ini $PHP_INI_DIR/conf.d/zz-php.ini
COPY vhosts.conf /opt/docker/etc/httpd/vhost.conf

# install extra packages
RUN apt-get update; \
	pecl install mongodb;\
	apt-get install openssl libssl-dev libcurl4-openssl-dev -y;

RUN apt-get update && apt-get install -y wget git unzip;

# enable apache mods.
RUN a2enmod rewrite
RUN a2enmod ssl
RUN service apache2 restart

# set arguments and start
ARG USER_ID
RUN usermod -u $USER_ID www-data