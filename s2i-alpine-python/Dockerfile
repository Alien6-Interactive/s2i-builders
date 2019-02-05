FROM alpine:3.8

EXPOSE 8080

# VERSIONS
ENV ALPINE_VERSION=3.8 \
    PYTHON_VERSION=3.7.2

ENV SUMMARY="Platform for building and running Python $PYTHON_VERSION applications" \
    DESCRIPTION="Python $PYTHON_VERSION available as container is a base platform for \
    building and running various Python $PYTHON_VERSION applications and frameworks. \
    Python is an easy to learn, powerful programming language. It has efficient high-level \
    data structures and a simple but effective approach to object-oriented programming. \
    Python's elegant syntax and dynamic typing, together with its interpreted nature, \
    make it an ideal language for scripting and rapid application development in many areas \
    on most platforms."

ENV APP_ROOT=/opt/app-root \
    STI_SCRIPTS_PATH=/usr/libexec/s2i \
    USR_LIB=/usr/lib

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.description="Alpine Python S2I Builder" \
      io.k8s.display-name="S2I Alpine Python" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,alpine,python" \
      io.openshift.s2i.scripts-url=image://${STI_SCRIPTS_PATH} \
      version="1" \
      usage="s2i build https://github.com/Alien6-Interactive/s2i-builders.git --context-dir=s2i-alpine-python/e2e/ alien6/s2i-alpine-python:0.1 python-sample-app" \
      maintainer="Alien6 <contact@alien6.com>"

# PATHS
ENV PYTHON_PATH=/usr/lib/python$PYTHON_VERSION \
    PATH="/usr/lib/python$PYTHON_VERSION/bin/:${PATH}" 

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
"
ENV PYTHON_BUILD_PACKAGES="\
    readline-dev \
    zlib-dev \
    bzip2-dev \
    sqlite-dev \
    openssl-dev \
"

RUN set -ex ;\
    mkdir -p ${APP_ROOT} ;\
    mkdir -p ${STI_SCRIPTS_PATH} ;\
    # find MAJOR and MINOR python versions based on $PYTHON_VERSION
    export PYTHON_MAJOR_VERSION=$(echo "${PYTHON_VERSION}" | rev | cut -d"." -f3-  | rev) ;\
    export PYTHON_MINOR_VERSION=$(echo "${PYTHON_VERSION}" | rev | cut -d"." -f2-  | rev) ;\
    # replacing default repositories with edge ones
    echo "http://dl-cdn.alpinelinux.org/alpine/v$ALPINE_VERSION/community" >> /etc/apk/repositories ;\
    echo "http://dl-cdn.alpinelinux.org/alpine/v$ALPINE_VERSION/main" >> /etc/apk/repositories ;\
    # Add the packages, with a CDN-breakage fallback if needed
    apk add --no-cache $PACKAGES || \
        (sed -i -e 's/dl-cdn/dl-4/g' /etc/apk/repositories && apk add --no-cache $PACKAGES) ;\
    # Add packages just for the python build process with a CDN-breakage fallback if needed
    apk add --no-cache --virtual .build-deps $PYTHON_BUILD_PACKAGES || \
        (sed -i -e 's/dl-cdn/dl-4/g' /etc/apk/repositories && apk add --no-cache --virtual .build-deps $PYTHON_BUILD_PACKAGES) ;\
    # turn back the clock -- so hacky!
    echo "http://dl-cdn.alpinelinux.org/alpine/v$ALPINE_VERSION/main/" > /etc/apk/repositories ;\
    # use pyenv to download and compile specific python version
    git clone --depth 1 https://github.com/pyenv/pyenv /usr/lib/pyenv ;\
    PYENV_ROOT=/usr/lib/pyenv /usr/lib/pyenv/bin/pyenv install $PYTHON_VERSION ;\
    # move specific version to correct path delete pyenv, no longer needed
    mv /usr/lib/pyenv/versions/$PYTHON_VERSION/ $PYTHON_PATH ;\
    rm -rfv /usr/lib/pyenv ;\
    # change the path on the header of every file from PYENV_ROOT to PYTHON_PATH
    cd $PYTHON_PATH/bin/ && sed -i "s+/usr/lib/pyenv/versions/$PYTHON_VERSION/+$PYTHON_PATH/+g" * ;\
    # delete binary "duplicates" and replace them with symlinks
    # this also optimizes space since they are actually the same binary
    rm -f $PYTHON_PATH/bin/python$PYTHON_MAJOR_VERSION \
          $PYTHON_PATH/bin/python$PYTHON_MINOR_VERSION \
          $PYTHON_PATH/bin/python$PYTHON_MAJOR_VERSION-config \
          $PYTHON_PATH/bin/python$PYTHON_MINOR_VERSION-config ;\
    ln -sf $PYTHON_PATH/bin/python $PYTHON_PATH/bin/python$PYTHON_MAJOR_VERSION ;\
    ln -sf $PYTHON_PATH/bin/python $PYTHON_PATH/bin/python$PYTHON_MINOR_VERSION ;\
    ln -sf $PYTHON_PATH/bin/python-config $PYTHON_PATH/bin/python$PYTHON_MAJOR_VERSION-config ;\
    ln -sf $PYTHON_PATH/bin/python-config $PYTHON_PATH/bin/python$PYTHON_MINOR_VERSION-config ;\
    # delete files to to reduce container size
    # tips taken from main python docker repo
    find /usr/lib/python$PYTHON_VERSION -depth \( -name '*.pyo' -o -name '*.pyc' -o -name 'test' -o -name 'tests' \) -exec rm -rf '{}' + ;\
    # remove build dependencies and any leftover apk cache
    apk del --no-cache --purge .build-deps ;\
    rm -rf /var/cache/apk/* ;\
    adduser -s /bin/sh -u 1001 -G root -h ${HOME} -S -D default ;\
    chown -R 1001:0 ${APP_ROOT} && chmod -R og+rwx ${APP_ROOT} ;\
    chown -R 1001:0 ${USR_LIB} ;

RUN pip install --upgrade pip 

# Add configuration files, bashrc and other tweaks
COPY ./s2i/bin/fix-permissions /usr/bin
COPY ./s2i/bin/ ${STI_SCRIPTS_PATH}

USER 1001

WORKDIR ${APP_ROOT}

# Set the default CMD to print the usage of the language image
CMD ${STI_SCRIPTS_PATH}/usage