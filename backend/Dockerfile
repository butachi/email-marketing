FROM php:8.3-fpm AS backend

WORKDIR /usr/src

ARG user
ARG uid

RUN apt-get update && apt-get install -y \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    git \
    curl \
    libc6 \
    zip \
    unzip \
    supervisor \
    default-mysql-client

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip 

RUN pecl install redis

COPY --from=composer:2.8.5 /usr/bin/composer /usr/bin/composer

RUN useradd -G www-data,root -u $uid -d /home/$user $user

RUN mkdir -p /home/$user/.composer && \
    chown -R $user:$user /home/$user

COPY ./backend/composer*.json /usr/src/
COPY ./deployment/config/php-fpm/php-prod.ini /usr/local/etc/php/conf.d/php.ini
COPY ./deployment/config/php-fpm/www.conf /usr/local/etc/php-fpm.d/www.conf
COPY ./deployment/bin/update.sh /usr/src/update.sh

RUN composer install --no-scripts

COPY ./backend .

RUN php artisan storage:link && \
    chmod +x ./update.sh && \
    chown -R $user:$user /usr/src && \
    chmod -R 775 ./storage ./bootstrap/cache

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install

USER $user

FROM backend AS worker
COPY ./deployment/config/supervisor/supervisord.conf /etc/supervisor/conf.d/supervisor.conf
CMD [ "/bin/sh" , "-c", "supervisord -c /etc/supervisor/conf.d/supervisor.conf"]

FROM backend AS scheduler 
CMD [ "/bin/sh", "-c", "nice -n 10 sleep 60 && php /usr/src/artisan schedule:run --verbose --no-interaction" ]