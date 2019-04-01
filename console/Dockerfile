FROM php:7.2.16-zts-alpine3.9

# install bash
RUN apk update && apk add bash vim

# install stuff for make pthreads
RUN apk update && apk add --no-cache \
    sudo bash \
    g++ make autoconf \
    libxml2-dev icu-dev curl-dev pcre-dev

# install mongodb
RUN pecl install mongodb && docker-php-ext-enable mongodb

# install pthreads
RUN curl -sSL https://github.com/krakjoe/pthreads/archive/master.zip -o /tmp/pthreads.zip \
    && unzip /tmp/pthreads.zip -d /tmp \
    && cd /tmp/pthreads-* \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && rm -rf /tmp/pthreads*

# enable pthreads and other modules
RUN docker-php-ext-install pcntl
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install pdo pdo_mysql

# memory limit
RUN echo 'memory_limit = 2048M' >> /usr/local/etc/php/conf.d/docker-php-memlimit.ini;

# fuck
RUN docker-php-ext-enable pthreads

# go and keep alive
CMD ["php", "-a"]

