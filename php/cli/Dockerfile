FROM skpr/php:latest

RUN apk --update --no-cache add \
      bash \
      curl \
      rsync

RUN curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    chmod +x /usr/local/bin/composer

CMD ["bash"]