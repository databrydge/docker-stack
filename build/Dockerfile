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

# we need this to trigger all errors just like production
RUN echo 'error_reporting=E_ALL' >> /usr/local/etc/php/conf.d/docker-php-display-errors.ini;
RUN echo 'date.timezone = "Europe/Amsterdam"'  > /usr/local/etc/php/conf.d/timezone.ini

# Install wkhtmltopdf
RUN wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.3/wkhtmltox-0.12.3_linux-generic-amd64.tar.xz
RUN tar vxf wkhtmltox-0.12.3_linux-generic-amd64.tar.xz
RUN cp wkhtmltox/bin/wk* /usr/local/bin/

# Install needed packages for wkhtmltopdf
RUN apk add --no-cache \
  libstdc++ \
  libx11 \
  libxrender \
  libxext \
  libssl1.1 \
  ca-certificates \
  fontconfig \
  freetype \
  ttf-dejavu \
  ttf-droid \
  ttf-freefont \
  ttf-liberation \
  ttf-ubuntu-font-family \
&& apk add --no-cache --virtual .build-deps \
  g++ \
  git \
  gtk+ \
  gtk+-dev \
  make \
  mesa-dev \
  msttcorefonts-installer \
  openssl-dev \
  patch \
  fontconfig-dev \
  freetype-dev \
\
# Install microsoft fonts
&& update-ms-fonts \
&& fc-cache -f