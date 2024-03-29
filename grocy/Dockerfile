ARG BUILD_FROM=ghcr.io/hassio-addons/base/amd64:11.0.0
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
# hadolint ignore=DL3003
RUN \
    apk add --no-cache \
        nginx=1.20.2-r0 \
        patch=2.7.6-r7 \
        php8-ctype=8.0.14-r0 \
        php8-exif=8.0.14-r0 \
        php8-fileinfo=8.0.14-r0 \
        php8-fpm=8.0.14-r0 \
        php8-gd=8.0.14-r0 \
        php8-iconv=8.0.14-r0 \
        php8-intl=8.0.14-r0 \
        php8-ldap=8.0.14-r0 \
        php8-mbstring=8.0.14-r0 \
        php8-opcache=8.0.14-r0 \
        php8-pdo_sqlite=8.0.14-r0 \
        php8-pdo=8.0.14-r0 \
        php8-simplexml=8.0.14-r0 \
        php8-tokenizer=8.0.14-r0 \
        php8=8.0.14-r0 \
    \
    && ln -s /usr/bin/php8 /usr/bin/php \
    \
    && apk add --no-cache --virtual .build-dependencies \
        git=2.34.1-r0 \
        php8-openssl=8.0.14-r0 \
        php8-phar=8.0.14-r0 \
        yarn=1.22.17-r0 \
    \
    && curl -sS https://getcomposer.org/installer \
        | php -- --install-dir=/usr/local/bin --filename=composer \
    \
    && yarn global add modclean \
    \
    && git clone --branch "v3.1.3" --depth=1 \
        https://github.com/llewy/grocy.git /var/www/grocy \
    \
    && cd /var/www/grocy \
    \
    && composer install --no-dev \
    && yarn --production \
    \
    && modclean \
        --path /var/www/grocy/public \
        --no-progress \
        --keep-empty \
        --run \
    \
    && yarn global remove modclean \
    && yarn cache clean \
    && apk del --no-cache --purge .build-dependencies \
    \
    && find /var/www/ -type f -name "*.md" -depth -exec rm -f {} \; \
    && rm -f -r \
        /tmp/* \
        /etc/nginx \
        /root/.composer \
        /usr/lib/node_modules \
        /usr/local/bin/composer \
        /usr/local/share/.cache \
        /var/www/grocy/.git \
        /var/www/grocy/.tx \
        /var/www/grocy/.vscode \
        /var/www/grocy/build_tools \
        /var/www/grocy/build.bat

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
