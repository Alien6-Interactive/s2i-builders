FROM alpine:3.8

# VERSIONS
ENV ALPINE_VERSION=3.8 

ENV SUMMARY="Platform for building and running Alpine Linux applications" \
    DESCRIPTION="Alpine Linux $ALPINE_VERSION available as container is a base platform for \
    building and running various Alpine Linux $ALPINE_VERSION applications and frameworks."

ENV APP_ROOT=/opt/app-root \
    STI_SCRIPTS_PATH=/usr/libexec/s2i

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.description="Alpine Linux S2I Builder" \
      io.k8s.display-name="S2I Alpine Linux" \
      io.openshift.tags="builder,alpine" \
      io.openshift.s2i.scripts-url=image://${STI_SCRIPTS_PATH} \
      version="1" \
      usage="s2i build https://github.com/Alien6-Interactive/s2i-builders.git --context-dir=s2i-base-alpine/e2e/ alien6/s2i-base-alpine:latest sample-app" \
      maintainer="Alien6 <contact@alien6.com>"

# PATHS
ENV PACKAGES="\
    dumb-init \
    musl \
    libc6-compat \
    linux-headers \
    build-base \
    bash \
    git \
    ca-certificates \
    libssl1.0 \
    libffi-dev \
    nss-tools \
"

RUN set -ex ;\
    mkdir -p ${APP_ROOT} ;\
    mkdir -p ${STI_SCRIPTS_PATH} ;\
    # replacing default repositories with edge ones
    echo "http://dl-cdn.alpinelinux.org/alpine/v$ALPINE_VERSION/community" >> /etc/apk/repositories ;\
    echo "http://dl-cdn.alpinelinux.org/alpine/v$ALPINE_VERSION/main" >> /etc/apk/repositories ;\
    # Add the packages, with a CDN-breakage fallback if needed
    apk add --no-cache $PACKAGES || \
        (sed -i -e 's/dl-cdn/dl-4/g' /etc/apk/repositories && apk add --no-cache $PACKAGES) ;\
    # turn back the clock -- so hacky!
    echo "http://dl-cdn.alpinelinux.org/alpine/v$ALPINE_VERSION/main/" > /etc/apk/repositories ;\
    rm -rf /var/cache/apk/* ;

RUN adduser -s /bin/sh -u 1001 -G root -h ${APP_ROOT} -S -D default ;\
    chown -R 1001:0 ${APP_ROOT} && chmod -R og+rwx ${APP_ROOT} ;\
    chown -R 1001:0 ${USR_LIB} ;\
    chown -R 1001:0 ${STI_SCRIPTS_PATH} ;

RUN ln -sf /bin/bash /bin/sh

# Add configuration files, bashrc and other tweaks
COPY ./s2i/bin/fix-permissions /usr/bin
COPY ./s2i/bin/ ${STI_SCRIPTS_PATH}

USER 1001

WORKDIR ${APP_ROOT}

# Set the default CMD to print the usage of the language image
CMD ${STI_SCRIPTS_PATH}/usage