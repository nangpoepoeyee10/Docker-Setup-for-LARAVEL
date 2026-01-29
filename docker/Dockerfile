# Use PHP 8.0
FROM php:8.2-fpm

ARG DEBIAN_FRONTEND=noninteractive

# Install common php extension dependencies
RUN apt-get update && apt-get install -y \
    bash \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    zlib1g-dev \
    libzip-dev \
    unzip \
    libonig-dev

# Install PHP extensions
# Install GD extension dependencies
RUN docker-php-ext-configure gd --with-freetype --with-jpeg

COPY docker/local/custom-php.ini /usr/local/etc/php/conf.d/custom-php.ini


# Install PHP extensions
RUN docker-php-ext-install -j$(nproc) pdo_mysql mbstring exif pcntl bcmath zip gd

# Set the working directory
WORKDIR /var/www/test-backend

# Copy source code
COPY . /var/www/test-backend

# Install composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Copy composer files and install dependencies
ENV COMPOSER_ALLOW_SUPERUSER=1
RUN composer install --no-scripts --no-autoloader

# Copy the rest of the application
COPY . .

# Run composer dump-autoload after copying application files
RUN composer dump-autoload --optimize

# Change ownership and permissions
RUN chown -R www-data:www-data /var/www/test-backend/storage && \
    chmod -R 775 /var/www/test-backend/storage

#create .env
RUN cp .env.example .env

#create key for .env
RUN php artisan key:generate

# Set the default command to run php-fpm
CMD ["php-fpm"]
