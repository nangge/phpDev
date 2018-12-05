FROM  php:7.2-fpm
COPY ./conf/php.ini /usr/local/etc/php/php.ini
  RUN apt-get update && apt-get install -y \
         libfreetype6-dev \
         libjpeg62-turbo-dev \
         libpng-dev \
         zlib1g-dev \
         wget \
         unzip \
         libzmq3-dev \
 && pecl install zmq-beta \
 && pecl install swoole \
 && pecl install redis-4.0.1 \
 && docker-php-ext-install pdo_mysql \
 && docker-php-ext-install mysqli \
 && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
 && docker-php-ext-install gd \
 && docker-php-ext-install zip 

