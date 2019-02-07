FROM docker:git

EXPOSE 8080

RUN apk add --no-cache git make musl-dev go bash

ENV SUMMARY="Platform for building and running golang applications" \
    DESCRIPTION="Golang available as container is a base platform for \
    building and running various Golang applications and frameworks."

# Configure Go
ENV APP_ROOT=/opt/app-root \
    STI_SCRIPTS_PATH=/usr/libexec/s2i \
    USR_LIB=/usr/lib \
	GOPATH=/go \
    GOBIN=/go/bin \
    SOURCE=$HOME/go/src/main \
    PATH=$PATH:$HOME/go/bin:/usr/local/go/bin

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.description="Alpine Golang S2I Builder" \
      io.k8s.display-name="S2I Alpine Golang" \
      io.openshift.tags="builder,alpine,git,docker, golang" \
      io.openshift.expose-services="8080:http" \
      io.openshift.s2i.scripts-url=image://${STI_SCRIPTS_PATH} \
      version="1" \
      usage="s2i build https://github.com/Alien6-Interactive/s2i-builders.git --context-dir=s2i-alpine-golang/e2e/ alien6/s2i-alpine-golang:0.1 golang-sample-app" \
      maintainer="Alien6 <contact@alien6.com>"

RUN export \
		GOROOT_BOOTSTRAP="$(go env GOROOT)" \
		GOOS="$(go env GOOS)" \
		GOARCH="$(go env GOARCH)" \
		GOHOSTOS="$(go env GOHOSTOS)" \
		GOHOSTARCH="$(go env GOHOSTARCH)" \
	; 

RUN export PATH="/usr/local/go/bin:$PATH"; \
	go version

RUN set -ex ;\
    mkdir -p ${APP_ROOT} ;\
    mkdir -p ${STI_SCRIPTS_PATH} ;

RUN adduser -s /bin/sh -u 1001 -G root -h ${APP_ROOT} -S -D default ;\
    chown -R 1001:0 ${APP_ROOT} && chmod -R og+rwx ${APP_ROOT} ;

RUN mkdir -p ${GOPATH}/src ${GOPATH}/bin
RUN mkdir -p "$GOPATH/src" "$GOPATH/bin" && chmod -R 777 "$GOPATH"

# Add configuration files, bashrc and other tweaks
COPY ./s2i/bin/fix-permissions /usr/bin
COPY ./s2i/bin/ ${STI_SCRIPTS_PATH}

USER 1001

WORKDIR $GOPATH

# Set the default CMD to print the usage of the language image
CMD ${STI_SCRIPTS_PATH}/usage