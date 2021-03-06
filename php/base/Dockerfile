FROM skpr/base:1.x

ARG PHP_VERSION=7.2

ENV NEW_RELIC_ENABLED=false
# ENV NEW_RELIC_APP_NAME=My Application
# ENV NEW_RELIC_LICENSE_KEY=xxxxxxxxxxxxxxxxx

ENV PHP_MEMORY_LIMIT=128M

ENV SYSLOG_SYMLINK=/mnt/syslog/log

ENV PATH "$PATH:/data/bin"

# https://github.com/moby/moby/issues/20295
RUN mkdir /data && \
    chown -R skpr:skpr /data

WORKDIR /data

RUN apk add --no-cache curl ca-certificates && \
    curl -L https://packages.skpr.io/php-alpine/skpr.rsa.pub -o /etc/apk/keys/skpr.rsa.pub && \
    echo "https://packages.skpr.io/php-alpine/${ALPINE_VERSION}/php${PHP_VERSION}" >> /etc/apk/repositories

RUN apk --update --no-cache add \
      php${PHP_VERSION} \
      php${PHP_VERSION}-apcu \
      php${PHP_VERSION}-bcmath \
      php${PHP_VERSION}-ctype \
      php${PHP_VERSION}-curl \
      php${PHP_VERSION}-dom \
      php${PHP_VERSION}-ftp \
      php${PHP_VERSION}-fileinfo \
      php${PHP_VERSION}-gd \
      php${PHP_VERSION}-iconv \
      php${PHP_VERSION}-json \
      php${PHP_VERSION}-mbstring \
      php${PHP_VERSION}-opcache \
      php${PHP_VERSION}-openssl \
      php${PHP_VERSION}-pcntl \
      php${PHP_VERSION}-pdo \
      php${PHP_VERSION}-pdo_mysql \
      php${PHP_VERSION}-pdo_sqlite \
      php${PHP_VERSION}-phar \
      php${PHP_VERSION}-posix \
      php${PHP_VERSION}-redis \
      php${PHP_VERSION}-session \
      php${PHP_VERSION}-simplexml \
      php${PHP_VERSION}-soap \
      php${PHP_VERSION}-sockets \
      php${PHP_VERSION}-sodium \
      php${PHP_VERSION}-sqlite3 \
      php${PHP_VERSION}-tokenizer \
      php${PHP_VERSION}-xml \
      php${PHP_VERSION}-xmlreader \
      php${PHP_VERSION}-xmlwriter \
      php${PHP_VERSION}-zip \
      pngquant \
      jpegoptim \
      mysql-client \
      optipng \
      tesseract-ocr \
      tesseract-ocr-dev \
      poppler-utils \
      libsodium


RUN if [ "$PHP_VERSION" != "7.4" ]; then \
    mv /etc/php/${PHP_VERSION}/* /etc/php/ && \
    rm -fR /etc/php/${PHP_VERSION} && \
    ln -sf /etc/php /etc/php/${PHP_VERSION} && \
    ln -sf /etc/php /etc/php7; \
fi

# New Relic
RUN export NR_INSTALL_SILENT=true && \
    export NR_INSTALL_USE_CP_NOT_LN=true && \
    export NR_VERSION=9.6.1.256 && \
    export NR_FILENAME=newrelic-php5-${NR_VERSION}-linux-musl && \
    curl -sSL https://download.newrelic.com/php_agent/archive/${NR_VERSION}/${NR_FILENAME}.tar.gz | gzip -dc | tar xf - && \
    cd ${NR_FILENAME} && ./newrelic-install install && \
    rm -fR /data/${NR_FILENAME} && \
    rm -f /etc/php/conf.d/newrelic.ini

RUN curl -sSL https://github.com/skpr/mail/releases/download/v0.0.6/skprmail_linux_amd64 -o /usr/local/bin/skprmail && \
    chmod +rx /usr/local/bin/skprmail

RUN curl -sSL https://s3.amazonaws.com/pnx-bins/docconv-v1.0.0-129-geedabc4-alpine${ALPINE_VERSION} -o /usr/local/bin/docconv && \
    chmod +rx /usr/local/bin/docconv

ADD conf.d/01_apcu.ini /etc/php/conf.d/01_apcu.ini
ADD conf.d/01_newrelic.ini /etc/php/conf.d/01_newrelic.ini
ADD conf.d/50_overrides.ini /etc/php/conf.d/50_overrides.ini

# Drush uses /tmp.
VOLUME /tmp
