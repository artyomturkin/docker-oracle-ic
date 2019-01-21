# docker-oracle-ic

Dockerfiles to build images with Oracle Instant client version 12.2.

## Building base images

To build this images you need to first copy Oracle Instant Client basic and sdk package archives from [Official site](https://www.oracle.com/technetwork/database/database-technologies/instant-client/overview/index.html) to packages directory.

Images were tested with Instant Client for Linux x86-64 version 12.2.0.1.0.

Resulting images were tested to work with `github.com/mattn/go-oci8` go package.

If building behind a proxy, provide --build-arg http_proxy=$http_proxy, where $http_proxy point to your proxy server.

### golang image
Image is built from golang:latest base image.

```sh
$ docker build -t golang-oracle-ic:latest -f dockerfiles/golang .
```

### ubuntu image
Image is built from ubuntu:xenial-20181218 base image

```sh
docker build -t ubuntu-oracle-ic:latest -f dockerfiles/ubuntu .
```

## Building application images
To create an images with go application, that depends on `github.com/mattn/go-oci8` (vendored), the following Dockerfile can be used:
```Dockerfile
FROM golang-oracle-ic:latest AS build-env
WORKDIR /go/src/<app-path>
ADD . /go/src/<app-path>/
RUN go build -o /go/bin/app .

FROM ubuntu-oracle-ic:latest
COPY --from=build-env /go/bin/app /
ENTRYPOINT ["/app"]
```
