FROM alien6/s2i-base-alpine:0.1

EXPOSE 8080

USER root

# When repository cannot be reached
RUN echo "http://dl-2.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories; \
    echo "http://dl-3.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories; \
    echo "http://dl-4.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories; \
    echo "http://dl-5.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories

RUN apk add --no-cache bash nodejs nodejs-npm ;

ENV SUMMARY="Platform for building and running node applications" \
    DESCRIPTION="Node available as container is a base platform for \
    building and running various Node applications and frameworks."

# Configure Go
ENV APP_ROOT=/opt/app-root \
    STI_SCRIPTS_PATH=/usr/libexec/s2i \
    USR_LIB=/usr/lib \
    NODEJS_VERSION=10 \
    NPM_RUN=start \
    NPM_CONFIG_PREFIX=$APP_ROOT/.npm-global \
    PATH=$APP_ROOT/node_modules/.bin/:$APP_ROOT/.npm-global/bin/:$PATH

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.description="Alpine Node S2I Builder" \
      io.k8s.display-name="S2I Alpine Node" \
      io.openshift.tags="builder,alpine,git,node" \
      io.openshift.expose-services="8080:http" \
      io.openshift.s2i.scripts-url=image://${STI_SCRIPTS_PATH} \
      version="1" \
      usage="s2i build https://github.com/Alien6-Interactive/s2i-builders.git --context-dir=s2i-alpine-node/e2e/ alien6/s2i-alpine-node:latest node-sample-app" \
      maintainer="Alien6 <contact@alien6.com>"

# Add configuration files, bashrc and other tweaks
COPY ./s2i/bin/fix-permissions /usr/bin
COPY ./s2i/bin/ ${STI_SCRIPTS_PATH}

USER 1001

WORKDIR $APP_ROOT

# Set the default CMD to print the usage of the language image
CMD ${STI_SCRIPTS_PATH}/usage