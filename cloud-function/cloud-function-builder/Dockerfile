ARG GOLANG_VERSION
FROM golang:$GOLANG_VERSION-alpine as build
RUN apk update
RUN apk add git curl
RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
COPY cloud-function-builder /go/src/github.com/GoogleCloudPlatform/cloud-builders-community/cloud-function/cloud-function-builder
WORKDIR /go/src/github.com/GoogleCloudPlatform/cloud-builders-community/cloud-function/cloud-function-builder
RUN dep ensure
RUN go install

FROM alpine
COPY --from=build /go/bin/cloud-function-builder /usr/local/bin
ENTRYPOINT ["cloud-function-builder"]