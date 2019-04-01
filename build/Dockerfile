FROM webdevops/php-apache:7.2

# set env
ENV WEB_DOCUMENT_ROOT /project/web/
ENV WEB_DOCUMENT_INDEX index.php

# copy php ini
COPY php.ini $PHP_INI_DIR/conf.d/zz-php.ini

# install extra packages
RUN apt-get update; \
	pecl install mongodb;\
	apt-get install openssl libssl-dev libcurl4-openssl-dev -y;

# enable apache mods.
RUN a2enmod rewrite
RUN a2enmod ssl
RUN service apache2 restart

# set arguments and start
ARG USER_ID
RUN usermod -u $USER_ID www-data

