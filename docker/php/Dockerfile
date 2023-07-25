# どんなdockerイメージを利用して構築をするか
FROM php:8.1-apache

RUN cd /etc/apache2/mods-enabled \
    && ln -s ../mods-available/rewrite.load
RUN apt-get update && apt-get install -y nodejs npm
RUN apt-get update && apt-get install -y build-essential


# 設定ファイルをdockerコンテナ内のPHP、Apacheに読み込ませる
ADD php.ini /usr/local/etc/php/
ADD 000-default.conf /etc/apache2/sites-enabled/

# Composerのインストール
RUN cd /usr/bin && curl -s http://getcomposer.org/installer | php && ln -s /usr/bin/composer.phar /usr/bin/composer

# ミドルウェアインストール
RUN apt-get update \
    && apt-get install -y \
    git \
    zip \
    unzip \
    libzip-dev \
    vim \
    libpng-dev \
    libpq-dev \
    libfreetype6-dev \
    libjpeg-dev \
    libonig-dev \
    && docker-php-ext-install pdo pdo_mysql mysqli zip

RUN docker-php-ext-configure gd \
    --with-freetype=/usr/include/ \
    --with-jpeg=/usr/include \
    && docker-php-ext-install -j$(nproc) gd

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /composer
ENV PATH $PATH:/composer/vendor/bin

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Update Composer to version 2
RUN composer self-update --2

# Install Node.js LTS (14.x) from the NodeSource repository
RUN curl -sL https://deb.nodesource.com/setup_lts.x | bash -
RUN apt-get install -y nodejs

# Clear npm cache
RUN npm cache clean --force

# Update npm
RUN npm install -g npm

# Delete node_modules folder
RUN rm -rf node_modules

# Rebuild node-sass
RUN npm rebuild node-sass

# Set the working directory
WORKDIR /var/www/
