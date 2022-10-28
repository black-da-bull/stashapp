# This dockerfile should be built with `make docker-build` from the stash root.

# Build Frontend
FROM node:alpine@sha256:1a04e2ec39cc0c3a9657c1d6f8291ea2f5ccadf6ef4521dec946e522833e87ea as frontend
RUN apk add --no-cache make
## cache node_modules separately
COPY ./ui/v2.5/package.json ./ui/v2.5/yarn.lock /stash/ui/v2.5/
WORKDIR /stash
RUN yarn --cwd ui/v2.5 install --frozen-lockfile.
COPY Makefile /stash/
COPY ./graphql /stash/graphql/
COPY ./ui /stash/ui/
RUN make generate-frontend
ARG GITHASH
ARG STASH_VERSION
RUN BUILD_DATE=$(date +"%Y-%m-%d %H:%M:%S") make ui

# Build Backend
FROM golang:1.17-alpine@sha256:99ddec1bbfd6d6bca3f9804c02363daee8c8524dae50df7942e8e60788fd17c9 as backend
RUN apk add --no-cache make alpine-sdk
WORKDIR /stash
COPY ./go* ./*.go Makefile gqlgen.yml .gqlgenc.yml /stash/
COPY ./scripts /stash/scripts/
COPY ./vendor /stash/vendor/
COPY ./pkg /stash/pkg/
COPY ./cmd /stash/cmd
COPY ./internal /stash/internal
COPY --from=frontend /stash /stash/
RUN make generate-backend
ARG GITHASH
ARG STASH_VERSION
RUN make build

# Final Runnable Image
FROM alpine:latest@sha256:bc41182d7ef5ffc53a40b044e725193bc10142a1243f395ee852a8d9730fc2ad
RUN apk add --no-cache ca-certificates vips-tools ffmpeg
COPY --from=backend /stash/stash /usr/bin/
ENV STASH_CONFIG_FILE=/root/.stash/config.yml
EXPOSE 9999
ENTRYPOINT ["stash"]
