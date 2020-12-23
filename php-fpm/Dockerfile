FROM php:7.0.33-fpm
# libjpeg-dev libfreetype6-dev gd扩展依赖包
# libmcrypt-dev  mcrypt扩展依赖包
# libbz2-dev bz2扩展依赖包
# libxml2-dev soap扩展依赖包
# libxslt-dev xsl扩展依赖包
# libmagickwand-6.q16-dev imagick扩展依赖包
# libc-client-dev libkrb5-dev imap扩展依赖包
# libldap2-dev ldap扩展依赖包
# libgmp-dev gmp扩展依赖包
# libfcgi0ldbl 通过cgi-fcgi实现健康检查
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "deb http://mirrors.aliyun.com/debian/ stretch main non-free contrib" > /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian/ stretch main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/debian-security stretch/updates main" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian-security stretch/updates main" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib" >> /etc/apt/sources.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
               wget \
               git \
               libjpeg-dev \
               libfreetype6-dev \
               libmcrypt-dev  \
               libbz2-dev \
               libmagickwand-6.q16-dev \
               libc-client-dev \
               libkrb5-dev \
               libldap2-dev \
               libgmp-dev \
               libxslt-dev \
               libfcgi0ldbl \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install calendar \
    && docker-php-ext-install exif \
    && docker-php-ext-configure gd  --enable-gd-native-ttf --with-jpeg-dir=/usr/lib --with-freetype-dir=/usr/include/freetype2 \
    && docker-php-ext-install gd \
    && docker-php-ext-install gettext \
    && docker-php-ext-install mcrypt \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install shmop \
    && docker-php-ext-install sockets \
    && docker-php-ext-install sysvmsg \
    && docker-php-ext-install sysvsem \
    && docker-php-ext-install sysvshm \
    && docker-php-ext-install zip \
    && docker-php-ext-install bz2 \
    && docker-php-ext-install soap \
    && docker-php-ext-install wddx \
    && docker-php-ext-install xmlrpc \
    && docker-php-ext-install xsl \
    && cd /tmp/ && git clone https://github.com/igbinary/igbinary "php-igbinary" \
    && cd php-igbinary \
    && phpize \
    && ./configure \
    && make install \
    && make clean \
    && docker-php-ext-enable igbinary \
    && ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin \
    && yes "" | pecl install -o -f imagick \
    && docker-php-ext-enable imagick \
    && yes "" | pecl install -o -f xlswriter \
    && docker-php-ext-enable xlswriter \
    && yes "" | pecl install -o -f http://pecl.php.net/get/swoole-4.3.0.tgz \
    && docker-php-ext-enable swoole \
    && yes "" | pecl install -o -f http://pecl.php.net/get/redis-3.1.0.tgz \
    && docker-php-ext-enable redis \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap \
    && docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ \
    && docker-php-ext-install ldap \
    && wget -O chilkat-9.5.0.83-php-7.0-x86_64-linux.tar.gz https://chilkatdownload.com/9.5.0.83/chilkat-9.5.0.83-php-7.0-x86_64-linux.tar.gz \
    && tar xf chilkat-9.5.0.83-php-7.0-x86_64-linux.tar.gz \
    && mv chilkat-9.5.0.83-php-7.0-x86_64-linux/chilkat_9_5_0.so /usr/local/lib/php/extensions/no-debug-non-zts-20151012/ \
    && echo "extension=chilkat_9_5_0.so" > /usr/local/etc/php/conf.d/docker-php-ext-chilkat_9_5_0.ini \
    && echo "zend_extension=opcache.so" > /usr/local/etc/php/conf.d/docker-php-ext-opcache.so.ini \
    && cd /tmp/ \
    && mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" \
    && sed -i 's/\(^memory_limit = \).*/\11024M/'  $PHP_INI_DIR/php.ini \
    && sed -i 's/\(^post_max_size = \).*/\150M/'  $PHP_INI_DIR/php.ini \
    && sed -i 's/\(^upload_max_filesize = \).*/\1100M/'  $PHP_INI_DIR/php.ini \
    && sed -i 's/;\(date.timezone =\)/\1 "Asia\/Shanghai"/' $PHP_INI_DIR/php.ini \
    && ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && dpkg-reconfigure -f noninteractive tzdata \
    && echo "<?php echo 'hello world'; ?>" > /status.php \
    && apt-get remove -y git wget && apt-get autoremove -y && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
ENV DEBIAN_FRONTEND=dialog
# 健康检查
HEALTHCHECK --interval=5s --timeout=3s \
    CMD SCRIPT_NAME=/status.php SCRIPT_FILENAME=/status.php REQUEST_METHOD=GET cgi-fcgi -bind -connect 127.0.0.1:9000 || exit 1
WORKDIR /var/www/html
EXPOSE 9000
CMD ["php-fpm"]