ARG BUILD_FROM

FROM $BUILD_FROM

# Environment variables
ENV \
    HOME="/root" \
    LANG="C.UTF-8" \
    PS1="$(whoami)@$(hostname):$(pwd)$ " \
    TERM="xterm"

RUN apk add --no-cache \
  bc \
  coreutils \
  curl \
  jq \
  dateutils \
  findutils

# Copy Node-RED package.json
COPY package.json requirements.txt /opt/

# Set workdir
WORKDIR /opt

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
RUN \
    apk add --no-cache --virtual .build-dependencies \
        g++=8.3.0-r0 \
        gcc=8.3.0-r0 \
        libc-dev=0.7.1-r0 \
        linux-headers=4.18.13-r1 \
        make=4.2.1-r2 \
        py2-pip=18.1-r0 \
        python2-dev=2.7.15-r3 \
    \
    && apk add --no-cache \
        git=2.20.1-r0 \
        lua-resty-http=0.12-r1 \
        nginx-mod-http-lua=1.14.2-r0 \
        nginx=1.14.2-r0 \
        nodejs=10.14.2-r0 \
        npm=10.14.2-r0 \
        openssh-client=7.9_p1-r5 \
        paxctl=0.9-r0 \
        python2=2.7.15-r3 \
    \
    && paxctl -cm "$(command -v node)" \
    \
    && npm config set unsafe-perm true \
    \
    && npm install -g modclean@3.0.0-beta.1 \
    \
    && pip install --no-cache-dir -r /opt/requirements.txt \
    \
    && npm install \
        --no-optional \
        --only=production \
    \
    && curl -J -L -o /tmp/theme.zip \
        "https://github.com/bonanitech/midnight-red/archive/v0.2.9.zip" \
    && unzip -d /tmp /tmp/theme.zip \
    && mkdir -p /etc/node-red \
    && mv /tmp/midnight-red-0.2.9/midnight.css /etc/node-red/ \
    && mv /tmp/midnight-red-0.2.9/theme-tomorrow.js /etc/node-red/ \
    \
    && modclean \
        --path /opt \
        --no-progress \
        --keep-empty \
        --run \
    \
    && npm uninstall -g modclean \
    && npm cache clear --force \
    \
    && apk del --no-cache --purge .build-dependencies \
    && rm -fr \
        /tmp/* \
        /etc/nginx

# Copy rootts
COPY rootfs /

CMD [ "/usr/bin/run.sh" ]

# Build arguments
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Labels
LABEL \
    org.label-schema.schema-version="1.0" \
    org.label-schema.build-date="${BUILD_DATE}" \
    org.label-schema.build-arch="${BUILD_ARCH}" \
    org.label-schema.name="nodered" \
    org.label-schema.description="PouchDB Server" \ 
    org.label-schema.vcs-url="http://github.com/dcmartin/open-horizon/tree/master/nodered/" \ 
    org.label-schema.vcs-ref="${BUILD_REF}" \ 
    org.label-schema.version="${BUILD_VERSION}" \
    org.label-schema.vendor="David C Martin <github@dcmartin.com>"
