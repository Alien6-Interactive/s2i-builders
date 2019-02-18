FROM ubuntu:18.04

# VERSIONS
ENV UBUNTU_VERSION=18.04

ENV SUMMARY="Platform for building and running shell scripts on Linux Ubuntu." \
    DESCRIPTION="Linux Ubuntu $UBUNTU_VERSION available as container is a base platform for \
    building and running various Linux Ubuntu scripts. "

ENV APP_ROOT=/opt/app-root \
    STI_SCRIPTS_PATH=/usr/libexec/s2i

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.description="Ubuntu S2I Builder" \
      io.k8s.display-name="S2I Ubuntu" \
      io.openshift.tags="builder,ubuntu" \
      io.openshift.s2i.scripts-url=image://${STI_SCRIPTS_PATH} \
      version="1" \
      usage="s2i build https://github.com/Alien6-Interactive/s2i-builders.git --context-dir=s2i-base-ubuntu/e2e/ alien6/s2i-base-ubuntu:latest sample-app" \
      maintainer="Alien6 <contact@alien6.com>"

ENV PACKAGES="\
    build-essential \
    bzip2 \
    git \
    procps \
    tar \
    unzip \
    wget \
"

RUN export DEBIAN_FRONTEND=noninteractive ;\
    apt-get -yq update ;\
    apt-get -yq install ${PACKAGES} ;\
    apt-get clean

RUN set -ex ;\
    mkdir -p ${APP_ROOT} ;\
    mkdir -p ${STI_SCRIPTS_PATH} ;\
    useradd -u 1001 -r -g 0 -d ${APP_ROOT} -s /sbin/nologin -c "Default Application User" default ;\
    chown -R 1001:0 ${APP_ROOT} 

# Add configuration files, bashrc and other tweaks
COPY ./s2i/bin/ ${STI_SCRIPTS_PATH}

USER 1001

WORKDIR ${APP_ROOT}

# Set the default CMD to print the usage of the language image
CMD ${STI_SCRIPTS_PATH}/usage