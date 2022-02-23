FROM ubuntu:18.04

LABEL maintainer="test"

RUN apt-get update \
    && apt-get install -y locales \
    && locale-gen en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

WORKDIR /var/www/html

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y nginx curl zip unzip git vim software-properties-common supervisor screen mysql-server \
    && apt-get install -y build-essential libmcrypt-dev libreadline-dev gnupg zlib1g-dev libzip-dev  libxml2-dev libpng-dev\
    && add-apt-repository -y ppa:ondrej/php \
    && apt-get update \
    && apt-get install -y gnumeric wget \
    && apt-get install -y php8.1-fpm php8.1-cli php8.1-gd php8.1-mysql \
       php8.1-pgsql php8.1-imap php-memcached php8.1-mbstring php8.1-xml php8.1-curl \
       php8.1-imagick php8.1-zip php8.1-bcmath php8.1-sqlite3 php8.1-xdebug php8.1-mongodb \
       php8.1-bcmath php8.1-soap php8.1-gd \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && mkdir /run/php \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && echo "daemon off;" >> /etc/nginx/nginx.conf

RUN apt-get update
RUN apt-get install nano

# install nodejs
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash -
RUN apt-get -y install nodejs

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log
RUN echo "uwsgi_read_timeout 300s;" > /etc/nginx/conf.d/custom_timeout.conf
COPY default /etc/nginx/sites-available/default
COPY php-fpm.conf /etc/php/8.1/fpm/php-fpm.conf
COPY xdebug.ini /etc/php/8.1/mods-available/xdebug.ini

COPY start-container /usr/local/bin/start-container
RUN chmod +x /usr/local/bin/start-container

RUN npm install --global cross-env

ADD uploads.ini /etc/php/8.1/cli/conf.d/uploads.ini

# Create the log file to be able to run tail
RUN touch /var/log/cron.log
COPY crontab /etc/cron.d/cool-task
RUN chmod 0644 /etc/cron.d/cool-task
RUN service cron start

RUN chmod -R 777 /var/www/

EXPOSE 80 8081

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

CMD ["start-container"]