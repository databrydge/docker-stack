FROM php:7.2.16-zts-alpine3.9

# install bash
RUN apk update && apk add bash vim

# fix work iconv library with alphine
RUN apk add --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/community/ --allow-untrusted gnu-libiconv
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php

# add pem file to system
RUN  wget http://curl.haxx.se/ca/cacert.pem -P /usr/local/share/ca-certificates/

# install stuff for make pthreads
RUN apk update && apk add --no-cache \
  sudo bash \
  g++ make autoconf \
  libxml2-dev icu-dev curl-dev pcre-dev

# install mongodb & xdebug
RUN pecl install mongodb && docker-php-ext-enable mongodb
RUN pecl install xdebug && docker-php-ext-enable xdebug

# install pthreads
RUN curl -sSL https://github.com/krakjoe/pthreads/archive/master.zip -o /tmp/pthreads.zip \
  && unzip /tmp/pthreads.zip -d /tmp \
  && cd /tmp/pthreads-* \
  && phpize \
  && ./configure \
  && make \
  && make install \
  && rm -rf /tmp/pthreads*

RUN apk add --no-cache \
  bzip2 \
  bzip2-dev \
  curl-dev

# enable pthreads and other modules
RUN docker-php-ext-install pdo pcntl bcmath bz2 calendar iconv intl mbstring mysqli opcache pdo_mysql soap zip sockets

RUN apk add --no-cache freetype libpng libjpeg-turbo freetype-dev libpng-dev libjpeg-turbo-dev && \
  docker-php-ext-configure gd \
  --with-gd \
  --with-freetype-dir=/usr/include/ \
  --with-png-dir=/usr/include/ \
  --with-jpeg-dir=/usr/include/ && \
  NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) && \
  docker-php-ext-install -j${NPROC} gd && \
  apk del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev

# display errors
RUN echo 'display_errors=on' >> /usr/local/etc/php/conf.d/docker-php-display-errors.ini;
RUN echo 'error_reporting=E_ALL' >> /usr/local/etc/php/conf.d/docker-php-display-errors.ini;

# memory limit
RUN echo 'memory_limit=3072M' >> /usr/local/etc/php/conf.d/docker-php-memlimit.ini;

# xdebug settings
RUN echo 'xdebug.idekey=phpstorm' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'xdebug.remote_autostart=1' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'xdebug.remote_enable=1' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'xdebug.default_enable=0' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'xdebug.remote_host=host.docker.internal' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'xdebug.remote_port=10000' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'xdebug.remote_connect_back=1' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'xdebug.profiler_enable=0' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'xdebug.remote_log="/tmp/xdebug.log"' >> /usr/local/etc/php/conf.d/docker-php-xdebug.ini;
RUN echo 'date.timezone = "Europe/Amsterdam"'  > /usr/local/etc/php/conf.d/timezone.ini

# set pem file location
RUN echo 'curl.cainfo = /usr/local/share/ca-certificates/cacert.pem' >> /usr/local/etc/php/conf.d/docker-php-cacert.ini;

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

# install git
RUN apk add --no-cache git

#Bash Aliases
RUN echo 'alias c="clear"' >> ~/.bashrc
RUN echo 'alias cdb="cd /databrydge"' >> ~/.bashrc
RUN echo 'alias cds="cd /dataswitcher"' >> ~/.bashrc

# set timezone
RUN apk add -U tzdata && \
  cp /usr/share/zoneinfo/Europe/Amsterdam /etc/localtime

# set error logging
RUN echo "php_admin_value[error_reporting] = E_ALL">>/usr/local/etc/php/www.conf

# fuck
RUN docker-php-ext-enable pthreads

# go and keep alive
CMD ["php", "-a"]
