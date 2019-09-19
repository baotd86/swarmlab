#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM php:7.1-fpm

LABEL maintainer="Terra Drone <bao.td@terra-drone.co.jp>"

# Set Environment Variables
ENV DEBIAN_FRONTEND noninteractive

# always run apt update when start and after add new source list, then clean up at end.
RUN apt-get update -y -qq && \
    apt-get install -y apt-utils && \
    pecl channel-update pecl.php.net

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#
# Installing tools and PHP extentions using "apt", "docker-php", "pecl",
#

# Install "curl", "libmemcached-dev", "libpq-dev", "libjpeg-dev",
#         "libpng12-dev", "libfreetype6-dev", "libssl-dev", "libmcrypt-dev",
RUN apt-get install -y -qq --no-install-recommends \
    curl \
    libmemcached-dev \
    libzip-dev \
    libpq-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libssl-dev \
  && rm -rf /var/lib/apt/lists/*

# Install the PHP mcrypt extention
RUN docker-php-ext-install pdo_mysql \
  # Install the PHP pdo_pgsql extention
  && docker-php-ext-install pdo_pgsql \
  # Install the PHP gd library
  && docker-php-ext-configure gd \
    --enable-gd-native-ttf \
    --with-jpeg-dir=/usr/lib \
    --with-freetype-dir=/usr/include/freetype2 && \
    docker-php-ext-install gd

###########################################################################
# ZipArchive:
###########################################################################

RUN apt-get install libzip-dev -y && \
    docker-php-ext-configure zip --with-libzip && \
    # Install the zip extension
    docker-php-ext-install zip

###########################################################################
# PHP REDIS EXTENSION
###########################################################################

RUN printf "\n" | pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis

###########################################################################
# PHP Memcached:
###########################################################################

RUN if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      curl -L -o /tmp/memcached.tar.gz "https://github.com/php-memcached-dev/php-memcached/archive/2.2.0.tar.gz"; \
    else \
      curl -L -o /tmp/memcached.tar.gz "https://github.com/php-memcached-dev/php-memcached/archive/php7.tar.gz"; \
    fi \
    && mkdir -p memcached \
    && tar -C memcached -zxvf /tmp/memcached.tar.gz --strip 1 \
    && ( \
        cd memcached \
        && phpize \
        && ./configure \
        && make -j$(nproc) \
        && make install \
    ) \
    && rm -r memcached \
    && rm /tmp/memcached.tar.gz \
    && docker-php-ext-enable memcached

###########################################################################
# bcmath:
###########################################################################

RUN docker-php-ext-install bcmath

###########################################################################
# GMP (GNU Multiple Precision):
###########################################################################

RUN apt-get update -y && \
	  apt-get install -y libgmp-dev && \
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h \
    ;fi && \
    docker-php-ext-install gmp

###########################################################################
# pgsql
###########################################################################

RUN docker-php-ext-install pgsql

###########################################################################
# pgsql client
###########################################################################

RUN mkdir -p /usr/share/man/man1 && \
    mkdir -p /usr/share/man/man7 && \
    # Install the pgsql client
    apt-get install -y postgresql-client

###########################################################################
# Tokenizer Modifications:
###########################################################################

RUN docker-php-ext-install tokenizer

###########################################################################
# Human Language and Character Encoding Support:
###########################################################################

RUN apt-get update -y && \
    apt-get install -y zlib1g-dev libicu-dev g++ && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl

###########################################################################
# LDAP:
###########################################################################

RUN apt-get install -y libldap2-dev && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap

###########################################################################
# ImageMagick:
###########################################################################

USER root

RUN apt-get update -y && \
    apt-get install -y libmagickwand-dev imagemagick && \
    pecl install imagick && \
    docker-php-ext-enable imagick

###########################################################################
# MCRYPT:
###########################################################################

USER root

RUN apt-get update -y && \
    apt-get install -y libmcrypt-dev && \
    pecl install mcrypt-1.0.0 && \
	  docker-php-ext-enable mcrypt.so

###########################################################################
# Check PHP version:
###########################################################################

RUN php -v | head -n 1 | grep -q "PHP 7.1."

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

USER root

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

RUN usermod -u 1000 www-data

WORKDIR /var/www/html

CMD ["php-fpm"]

EXPOSE 9000
