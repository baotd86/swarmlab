#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM phusion/baseimage:latest

LABEL maintainer="terra-drone.net"

RUN DEBIAN_FRONTEND=noninteractive
RUN locale-gen en_US.UTF-8

ENV LANGUAGE=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV LC_CTYPE=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV TERM xterm

# Add the "PHP 7" ppa
RUN apt-get install -y software-properties-common && \
    add-apt-repository -y ppa:ondrej/php

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

# Install "PHP Extentions", "libraries", "Software's"
RUN apt-get update && \
    apt-get install -y --allow-downgrades --allow-remove-essential \
        --allow-change-held-packages \
        php7.1-cli \
        php7.1-common \
        php7.1-curl \
        php7.1-intl \
        php7.1-json \
        php7.1-xml \
        php7.1-mbstring \
        php7.1-mcrypt \
        php7.1-mysql \
        php7.1-pgsql \
        php7.1-sqlite \
        php7.1-sqlite3 \
        php7.1-zip \
        php7.1-bcmath \
        php7.1-memcached \
        php7.1-gd \
        php7.1-dev \
        pkg-config \
        libcurl4-openssl-dev \
        libedit-dev \
        libssl-dev \
        libxml2-dev \
        xz-utils \
        libsqlite3-dev \
        sqlite3 \
        git \
        curl \
        vim \
        nano \
        postgresql-client \
    && apt-get clean

# Start as root
USER root

###########################################################################
# drondocker non-root user:
###########################################################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ENV PUID 1000
ENV PGID 1000

# always run apt update when start and after add new source list, then clean up at end.
RUN apt-get update -yqq && \
    pecl channel-update pecl.php.net && \
    groupadd -g 1000 lempdock && \
    useradd -u 1000 -g lempdock -m lempdock -G docker_env && \
    usermod -p "*" lempdock

###########################################################################
# Set Timezone
###########################################################################

ENV TZ UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo UTC > /etc/timezone

###########################################################################
# User Aliases
###########################################################################

USER root

COPY ./aliases.sh /root/aliases.sh
COPY ./aliases.sh /home/lempdock/aliases.sh

RUN sed -i 's/\r//' /root/aliases.sh && \
    sed -i 's/\r//' /home/lempdock/aliases.sh && \
    chown -R lempdock:lempdock /home/lempdock/aliases.sh && \
    echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source ~/aliases.sh" >> ~/.bashrc && \
	echo "" >> ~/.bashrc

USER lempdock

RUN echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source ~/aliases.sh" >> ~/.bashrc && \
	echo "" >> ~/.bashrc

#####################################
# Composer:
#####################################

USER root

# Add the composer.json
COPY ./composer.json /home/lempdock/.composer/composer.json

# Install composer and add its bin to the PATH.
RUN curl -s http://getcomposer.org/installer | php && \
    echo "export PATH=${PATH}:/var/www/vendor/bin" >> ~/.bashrc && \
    mv composer.phar /usr/local/bin/composer

# Make sure that ~/.composer belongs to lempdock
RUN chown -R lempdock:lempdock /home/lempdock/.composer
RUN . ~/.bashrc

USER lempdock

RUN composer global install

ENV COMPOSER_REPO_PACKAGIST http://packagist.org

RUN composer config -g repo.packagist composer http://packagist.org/

# Export composer vendor path
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="~/.composer/vendor/bin:$PATH"' >> ~/.bashrc

###########################################################################
# Non-root user : PHPUnit path
###########################################################################

# add ./vendor/bin to non-root user's bashrc (needed for phpunit)
USER lempdock

RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="/var/www/vendor/bin:$PATH"' >> ~/.bashrc


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

# Set default work directory
WORKDIR /var/www/html
