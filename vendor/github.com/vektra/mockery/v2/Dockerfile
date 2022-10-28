FROM golang:1.16-alpine@sha256:5616dca835fa90ef13a843824ba58394dad356b7d56198fb7c93cbe76d7d67fe as builder

COPY mockery /usr/local/bin

# Explicitly set a writable cache path when running --user=$(id -u):$(id -g)
# see: https://github.com/golang/go/issues/26280#issuecomment-445294378
ENV GOCACHE /tmp/.cache

ENTRYPOINT ["/usr/local/bin/mockery"]
