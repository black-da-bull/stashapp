FROM golang:1.16-alpine3.13@sha256:c538c29503b9ac4b874ae776a0537fe16fde4581af896d112e53a44a9963b116 AS builder
ARG VERSION

RUN apk add --no-cache git gcc musl-dev make

WORKDIR /go/src/github.com/golang-migrate/migrate

ENV GO111MODULE=on

COPY go.mod go.sum ./

RUN go mod download

COPY . ./

RUN make build-docker

FROM alpine:3.13@sha256:100448e45467d4f3838fc8d95faab2965e22711b6edf67bbd8ec9c07f612b553

RUN apk add --no-cache ca-certificates

COPY --from=builder /go/src/github.com/golang-migrate/migrate/build/migrate.linux-386 /usr/local/bin/migrate
RUN ln -s /usr/local/bin/migrate /migrate

ENTRYPOINT ["migrate"]
CMD ["--help"]
