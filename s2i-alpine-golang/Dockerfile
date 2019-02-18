FROM alien6/s2i-base-alpine:latest

EXPOSE 8080

USER root

# When repository cannot be reached
RUN echo "http://dl-2.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories; \
    echo "http://dl-3.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories; \
    echo "http://dl-4.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories; \
    echo "http://dl-5.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories

RUN apk add --no-cache bash gcc musl-dev openssl go ;

ENV SUMMARY="Platform for building and running golang applications" \
    DESCRIPTION="Golang available as container is a base platform for \
    building and running various Golang applications and frameworks."

# Configure Go
ENV STI_SCRIPTS_PATH=/usr/libexec/s2i \
    USR_LIB=/usr/lib \
    GOROOT=/usr/lib/go \
    GOPATH=/go \ 
    GOBIN=/go/bin \
    SOURCE=$HOME/go/src \
    PATH=/go/bin:/usr/local/go/bin:$PATH

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.description="Alpine Golang S2I Builder" \
      io.k8s.display-name="S2I Alpine Golang" \
      io.openshift.tags="builder,alpine,git,golang" \
      io.openshift.expose-services="8080:http" \
      io.openshift.s2i.scripts-url=image://${STI_SCRIPTS_PATH} \
      version="1" \
      usage="s2i build https://github.com/Alien6-Interactive/s2i-builders.git --context-dir=s2i-alpine-golang/e2e/ alien6/s2i-alpine-golang:latest golang-sample-app" \
      maintainer="Alien6 <contact@alien6.com>"

RUN export \
		GOROOT_BOOTSTRAP="$(go env GOROOT)" \
		GOOS="$(go env GOOS)" \
		GOARCH="$(go env GOARCH)" \
		GOHOSTOS="$(go env GOHOSTOS)" \
		GOHOSTARCH="$(go env GOHOSTARCH)" \
	; 

RUN export PATH="$PATH"; \
	go version

RUN mkdir -p "$SOURCE" "$GOBIN" && \
    chmod -R 777 "$GOPATH" && \
    chmod -R 777 "$SOURCE" && \
    chmod -R 777 "$USR_LIB"

# Add configuration files, bashrc and other tweaks
COPY ./s2i/bin/fix-permissions /usr/bin
COPY ./s2i/bin/ "$STI_SCRIPTS_PATH"

USER 1001

WORKDIR $SOURCE

# Set the default CMD to print the usage of the language image
CMD ["${STI_SCRIPTS_PATH}/usage"]