FROM php:{%version%}-cli-stretch

ENV PHP_MEMORY_LIMIT=2G \
    MAGENTO_ROOT=/app \
    DEBUG=false \
    MAGENTO_RUN_MODE=production \
    COMPOSER_ALLOW_SUPERUSER=1
{%env_php_extensions%}

# Configure Node.js version
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash

# Install dependencies
RUN apt-get update \
  && apt-get upgrade -y \
  && apt-get install -y --no-install-recommends \
  {%packages%} \
  && rm -rf /var/lib/apt/lists/*

# Install PyYAML
RUN pip3 install --upgrade setuptools \
    && pip3 install pyyaml

# Install Grunt
RUN npm install -g grunt-cli

# Configure the gd library
{%docker-php-ext-configure%}

# Install required PHP extensions
{%docker-php-ext-install%}

{%php-pecl-extensions%}

{%installation_scripts%}

{%docker-php-ext-enable%}

ADD etc/php-cli.ini /usr/local/etc/php/conf.d/zz-magento.ini
ADD etc/php-xdebug.ini /usr/local/etc/php/conf.d/zz-xdebug-settings.ini
ADD etc/mail.ini /usr/local/etc/php/conf.d/zz-mail.ini

VOLUME /root/.composer/cache
# Get composer installed to /usr/local/bin/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

ADD bin/* /usr/local/bin/
ADD docker-entrypoint.sh /docker-entrypoint.sh

RUN ["chmod", "+x", \
    "/docker-entrypoint.sh", \
    "/usr/local/bin/magento-installer", \
    "/usr/local/bin/magento-command", \
    "/usr/local/bin/ece-command", \
    "/usr/local/bin/cloud-build", \
    "/usr/local/bin/cloud-deploy", \
    "/usr/local/bin/cloud-post-deploy", \
    "/usr/local/bin/run-cron", \
    "/usr/local/bin/run-hooks" \
]

ENTRYPOINT ["/docker-entrypoint.sh"]

WORKDIR ${MAGENTO_ROOT}

CMD ["bash"]
