FROM nginx:alpine

LABEL maintainer="Bao Tran <bao@baotran.me>"

COPY nginx.conf /etc/nginx/

COPY default.conf /etc/nginx/sites-available

RUN mkdir /var/www/html

COPY app.conf /etc/nginx/sites-enabled

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/' /etc/apk/repositories

RUN apk update \
    && apk upgrade \
    && apk add --no-cache bash \
    && adduser -D -H -u 1000 -s /bin/bash www-data

# Set upstream conf and remove the default conf
RUN echo "upstream php-upstream { server php-fpm:9000; }" > /etc/nginx/conf.d/upstream.conf \
    && rm /etc/nginx/conf.d/default.conf

# RUN rm /etc/nginx/conf.d/default.conf
#   && htpasswd -c /etc/nginx/.htpasswd drondocker
CMD ["nginx"]

EXPOSE 80 443
