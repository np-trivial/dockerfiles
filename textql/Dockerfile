FROM ghcr.io/jauderho/golang:1.24.0-alpine3.21@sha256:157489c5bfe3d9b577009dcc142e1323666ea25d42353d9adabdd434c8a0a007 AS build

WORKDIR /go/src/github.com/dinedal/textql/

ARG BUILD_VERSION
ARG ARCHIVE_URL=https://github.com/dinedal/textql/archive/
ARG GIT_URL=https://github.com/dinedal/textql.git

ENV CGO_ENABLED 1

RUN test -n "${BUILD_VERSION}" \
	&& apk update \
	&& apk upgrade -a \
	&& apk add --no-cache git gcc musl-dev sqlite-dev \ 
	&& git clone --depth 1 ${GIT_URL} /go/src/github.com/dinedal/textql \
	&& cd /go/src/github.com/dinedal/textql \
	&& CGO_CFLAGS="-D_LARGEFILE64_SOURCE" go build -v -o textql-cli -trimpath -ldflags="-s -w" ./textql

WORKDIR /config


# ----------------------------------------------------------------------------


#FROM scratch
FROM ghcr.io/jauderho/alpine:3.21.3@sha256:1b1fc889769b514ff40409425a8ef8e6718c2731e5b87409bef393ed05e6d3da

LABEL org.opencontainers.image.authors="Jauder Ho <jauderho@users.noreply.github.com>"
LABEL org.opencontainers.image.url="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.documentation="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.source="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.title="jauderho/textql"
LABEL org.opencontainers.image.description="textql allows you to easily execute SQL against structured text like CSV or TSV"

RUN apk update \
	&& apk upgrade -a \
	&& apk add --no-cache sqlite

COPY --from=build /go/src/github.com/dinedal/textql/textql-cli /usr/local/bin/textql
COPY --from=build /config /config

WORKDIR /tmp

ENTRYPOINT ["/usr/local/bin/textql"]

