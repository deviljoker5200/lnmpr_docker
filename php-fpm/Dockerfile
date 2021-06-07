FROM php:7.4-fpm

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN sed -i 's/deb.debian.org/mirrors.163.com/' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.163.com/' /etc/apt/sources.list && \
    sed -i 's/security-cdn.debian.org/mirrors.163.com/' /etc/apt/sources.list

RUN set -eux; \
    apt-get update; \
    apt-get upgrade -y; \
    apt-get install -y --no-install-recommends \
            curl \
            libmemcached-dev \
            libz-dev \
            libpq-dev \
            libjpeg-dev \
            libpng-dev \
            libfreetype6-dev \
            libssl-dev \
            libwebp-dev \
            libmcrypt-dev \
            libonig-dev; \
    rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    # Install the PHP pdo_mysql extention
    docker-php-ext-install pdo_mysql; \
    # Install the PHP pdo_pgsql extention
    docker-php-ext-install pdo_pgsql; \
    # Install the PHP gd library
    docker-php-ext-configure gd \
            --prefix=/usr \
            --with-jpeg \
            --with-webp \
            --with-freetype; \
    docker-php-ext-install gd; \
    php -r 'var_dump(gd_info());'

RUN set -xe; \
    apt-get update -yqq && \
    pecl channel-update pecl.php.net && \
    apt-get install -yqq \
      apt-utils \
      gnupg2 \
      git \
      vim \
      #backup:run need mysqldump
      mariadb-client \
      #
      #--------------------------------------------------------------------------
      #--------------------------------------------------------------------------
      # Mandatory Software's Installation
      #--------------------------------------------------------------------------
      #
      # Mandatory Software's such as ("mcrypt", "pdo_mysql", "libssl-dev", ....)
      # are installed on the base image 'laradock/php-fpm' image. If you want
      # to add more Software's or remove existing one, you need to edit the
      # base image (https://github.com/Laradock/php-fpm).
      #
      # next lines are here becase there is no auto build on dockerhub see https://github.com/laradock/laradock/pull/1903#issuecomment-463142846
      libzip-dev zip unzip && \
      docker-php-ext-configure zip && \
      # Install the zip extension
      docker-php-ext-install zip && \
      php -m | grep -q 'zip'

RUN pecl install -o -f redis \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis

RUN docker-php-ext-install mysqli; \
    docker-php-ext-install sockets

RUN apt-get install -yqq zlib1g-dev libicu-dev g++ && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl

COPY php.ini "/usr/local/etc/php/php.ini"

RUN apt-get install -y libmagickwand-dev libmagickcore-dev && \
    pecl config-set php_ini "/usr/local/etc/php/php.ini" && \
    printf "\n" | pecl install -f imagick && \
    echo "extension=imagick.so" >> "/usr/local/etc/php/php.ini"

RUN docker-php-ext-enable imagick && \
    php -m | grep -q 'imagick'

RUN apt-get install -yqq supervisor

WORKDIR /var/www/html

CMD ["php-fpm"]

EXPOSE 9000
#
#COPY ./supervisor/supervisord.conf "/etc/supervisor/supervisord.conf"

#ENTRYPOINT ["/usr/bin/supervisord", "-c","/etc/supervisor/supervisord.conf"]