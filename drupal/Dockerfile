FROM php:5.6-apache

RUN apt-get update && apt-get install -y --no-install-recommends \
	libfreetype6-dev \
	libjpeg62-turbo-dev \
	libmcrypt-dev \
	libpng12-dev

RUN docker-php-ext-install json iconv mysql mcrypt pdo_mysql zip mbstring \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install gd

COPY config/php.ini /usr/local/etc/php/

# Apache + PHP requires preforing Apache for best results
RUN a2dismod mpm_event && a2enmod mpm_prefork

RUN a2enmod rewrite

# Install Composer.
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
	&& chmod 755 /usr/local/bin/composer \
	&& ln -s $HOME/.composer/vendor/bin/drush /usr/local/bin/drush

# Install Drush
RUN composer global require drush/drush:8.* \
	&& composer global update 

COPY src /var/www/html

WORKDIR /var/www/html

EXPOSE 80