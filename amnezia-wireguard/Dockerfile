ARG BUILD_FROM=ghcr.io/hassio-addons/base:17.2.1
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Amnezia Wireguart Tools version
ENV AWG_TOOLS=v1.0.20241018
# Amnezia WG GO version
ENV AWG_GO=v0.2.12

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
# hadolint ignore=DL3003
# RUN echo "nameserver 8.8.8.8" > /etc/resolv.conf
# RUN apk add --no-cache ca-certificates && update-ca-certificates
# RUN set -ex && \
#     ping -c 4 google.com && \
#     apk update && \
#     apk add --no-cache alpine-sdk linux-headers git
# RUN set -ex && \
#     echo "https://dl-cdn.alpinelinux.org/alpine/latest-stable/main" > /etc/apk/repositories && \
#     echo "https://dl-cdn.alpinelinux.org/alpine/latest-stable/community" >> /etc/apk/repositories && \
#     apk update && \
#     apk add --no-cache alpine-sdk linux-headers git || \
#     (echo "Ошибка: не удалось установить пакеты" >&2 && exit 1)
RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base \
        linux-headers \
        git
RUN \
    apk add --no-cache \
        go \
        iptables \
        libqrencode-tools \
        openresolv
RUN \
    ln -sf /sbin/xtables-nft-multi /sbin/ip6tables \
    && ln -sf /sbin/xtables-nft-multi /sbin/iptables \
    && git clone --depth=1 --branch $AWG_TOOLS \
        "https://github.com/amnezia-vpn/amneziawg-tools.git" /tmp/wg-tools \
    && cd /tmp/wg-tools/src \
    && make \
    && make install
RUN \
    git clone --depth=1 --branch $AWG_GO\
        "https://github.com/amnezia-vpn/amneziawg-go.git" /tmp/wireguard \
    \
    && cd /tmp/wireguard \
    && make \
    && make install \
    \
    && rm -f -r /tmp/* \
    && apk del --no-cache --purge \
        .build-dependencies \
        go

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
    maintainer="fork Franck Nijhof <frenck@addons.community>" \
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

