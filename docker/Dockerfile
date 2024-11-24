FROM php:8.2-fpm

# Install system dependencies
RUN apt-get update && apt-get install -y \
    libzip-dev \
    unzip \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libicu-dev \
    libpq-dev \
    libjpeg-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    libxslt1-dev \
    libyaml-dev \
    libmagickwand-dev --no-install-recommends && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd intl zip opcache

# Install Xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug

# Create Xdebug configuration file
RUN echo "zend_extension=xdebug.so" > /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.client_port=9003" >> /usr/local/etc/php/conf.d/xdebug.ini

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Install Symfony CLI
RUN curl -sS https://get.symfony.com/cli/installer | bash && \
    mv /root/.symfony*/bin/symfony /usr/local/bin/symfony

# Install Node.js and npm
RUN curl -fsSL https://deb.nodesource.com/setup_16.x | bash - && \
    apt-get install -y nodejs

# Arguments to create a user with specific UID and username
ARG USER_ID
ARG USER_NAME

# Create a user, add /data directory for persisting files, and change ownership
RUN adduser --uid $USER_ID --disabled-password --gecos "" $USER_NAME \
    && mkdir -p /data \
    && chown -R $USER_NAME:$USER_NAME /var/www/html /data

# Copy php.ini to the CLI configuration directory
RUN cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini

# Set the working directory
WORKDIR /var/www/html/symfony

# Switch to the newly created user
USER $USER_NAME

# Expose port 9000
EXPOSE 9000

# Start PHP-FPM
CMD ["php-fpm"]
