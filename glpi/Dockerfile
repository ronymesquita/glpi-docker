FROM php:7.4.4-apache-buster
COPY config/php.ini $PHP_INI_DIR/conf.d/

RUN apt-get update && apt-get install -y \
    zlib1g-dev \
    libwebp-dev \
    libxpm-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libpng-dev \
    libldb-dev \
    libldap2-dev \
    libc-client-dev \
    libxml2-dev \
    libkrb5-dev \
    wget \
    at \
    zip

# Needed to use CAS
# libapache2-mod-auth-cas

RUN pecl install apcu
RUN docker-php-ext-enable apcu
RUN docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg --with-webp --with-xpm
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl
RUN docker-php-ext-install gd mysqli ldap imap xmlrpc opcache exif

RUN wget https://github.com/glpi-project/glpi/releases/download/9.4.5/glpi-9.4.5.tgz \
    && tar -xf glpi-9.4.5.tgz \
    && cp -R glpi/* . \
    && rm -rf glpi \
    && rm glpi-9.4.5.tgz \
    && perl -p -i -e 's/post_max_size = 8M/post_max_size = 64M/g' $PHP_INI_DIR/php.ini-* \
    && perl -p -i -e 's/upload_max_filesize = 2M/upload_max_filesize = 32M/g' $PHP_INI_DIR/php.ini-* \
    && chown -R www-data:www-data * \
    && service atd start \
    && echo "rm install/install.php" | at -m now + 12 minute
