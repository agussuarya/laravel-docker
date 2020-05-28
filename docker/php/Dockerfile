FROM php:7.2-fpm-alpine

# Copy composer.lock and composer.json
COPY composer.lock composer.json /var/www/laravel/

# Set working directory
WORKDIR /var/www/laravel

# Install dev dependencies
RUN apk add --no-cache --virtual .build-deps \
$PHPIZE_DEPS \
curl-dev \
imagemagick-dev \
libtool \
libxml2-dev \
sqlite-dev

# Install dependencies
RUN apk add --no-cache \
bash \
curl \
g++ \
gcc \
git \
imagemagick \
libc-dev \
libpng-dev \
make \
mysql-client \
nodejs \
nodejs-npm \
yarn \
openssh-client \
zlib-dev \
libzip-dev \
tzdata

# Install PECL and PEAR extensions
RUN pecl list-all

RUN pecl install \
imagick \
xdebug \
redis

# Install and enable php extensions
RUN docker-php-ext-enable \
imagick \
xdebug \
redis
RUN docker-php-ext-configure zip --with-libzip
RUN docker-php-ext-install \
curl \
iconv \
mbstring \
pdo \
pdo_mysql \
pcntl \
tokenizer \
xml \
gd \
zip \
bcmath

# Cleanup dev dependencies
RUN apk del -f .build-deps

# Set timezone
ENV TZ Asia/Singapore

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Clear cache
RUN rm -rf /var/lib/apt/lists/*

# Copy existing application directory contents
COPY . /var/www/laravel

# Copy existing application directory permissions
COPY --chown=www-data:www-data . /var/www/laravel

# Change current user to www
USER www-data

# Cron
USER root
COPY docker/scheduler/artisan-schedule-run /var/spool/cron/crontabs/root
RUN touch /var/log/cron.log
