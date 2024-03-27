FROM php:8.1-fpm

# Set working directory
WORKDIR /var/www/html

# Add docker php ext repo
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

# Install php extensions
RUN chmod +x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions imagick mbstring pdo_mysql zip exif pcntl gd memcached intl

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    unzip \
    git \
    curl \
    lua-zlib-dev \
    libmemcached-dev \
    nginx

RUN docker-php-ext-install opcache

# Install supervisor
RUN apt-get install -y supervisor

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN apt-get update && \
    apt-get install -y \
    nodejs npm

RUN curl -fsSL https://deb.nodesource.com/setup_current.x | bash - && \
    apt-get install -y nodejs

RUN npm install --global yarn

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy code to /var/www/html
COPY --chown=www:www-data . /var/www/html

# add root to www group
RUN chmod -R ug+w /var/www/html/storage

# Copy nginx/php/supervisor configs
RUN cp docker/dev/supervisor.conf /etc/supervisord.conf
RUN cp docker/dev/php.ini /usr/local/etc/php/conf.d/app.ini
RUN cp docker/dev/nginx.conf /etc/nginx/sites-enabled/default
RUN cp docker/dev/php/conf.d/opcache.ini /usr/local/etc/php/conf.d/opcache.ini
RUN cat docker/dev/openssl.cnf >> /usr/lib/ssl/openssl.cnf

# PHP Error Log Files
RUN mkdir /var/log/php
RUN touch /var/log/php/errors.log && chmod 777 /var/log/php/errors.log

RUN chmod +x /var/www/html/docker/dev/run.sh

EXPOSE 80
ENTRYPOINT ["sh","/var/www/html/docker/dev/run.sh"]