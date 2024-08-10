# Used for production build.
FROM php:8.3-fpm

# Install dependencies.
RUN apt-get update && apt-get install -y unzip libpq-dev libcurl4-gnutls-dev nginx libonig-dev default-mysql-client libzip-dev zip cron

# Install Node.js
RUN apt-get update && apt-get install -y curl gnupg && curl -sL https://deb.nodesource.com/setup_20.x | bash - && apt-get install -y nodejs

# Install dependencies for optimizing image
RUN apt-get update && apt-get install -y jpegoptim optipng pngquant gifsicle webp
# Install SVGO globally using npm
RUN npm install -g svgo

# Install PHP extensions.
RUN docker-php-ext-install mysqli pdo pdo_mysql bcmath curl mbstring opcache pcntl zip

# Install Redis
RUN pecl install -o -f redis && rm -rf /tmp/pear && docker-php-ext-enable redis

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Copy composer executable.
COPY --from=composer:2.6 /usr/bin/composer /usr/bin/composer

ARG APP_ENV=local

COPY configurations /configurations

COPY scripts /scripts
RUN chmod +x /scripts/*
RUN /bin/bash -c '/scripts/configuration.sh'

# CRON
# https://github.com/yupmin/laravel-docker-cron/blob/master/Dockerfile
COPY crontab /etc/cron.d/laravel-cron
RUN chmod 0644 /etc/cron.d/laravel-cron
RUN touch /var/log/cron.log

# Set working directory to /var/www/html.
WORKDIR /var/www/html

# Copy existing application directory permissions
COPY --chown=www-data:www-data . .

# Adjust user permission & group
RUN usermod --uid 1000 www-data
RUN groupmod --gid 1001 www-data



