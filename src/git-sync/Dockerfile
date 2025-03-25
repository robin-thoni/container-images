# syntax=docker/dockerfile:1

FROM golang:1.22 AS builder

SHELL ["/bin/bash", "-c"]

ARG IMAGE_VERSION

RUN <<EOF
set -x
set -e
set -o pipefail

git clone https://github.com/kubernetes/git-sync
cd git-sync
git checkout "v$(echo "${IMAGE_VERSION}" | sed -E 's/-[0-9]+$//')"
EOF

WORKDIR /go/git-sync

RUN <<EOF
set -x
set -e
set -o pipefail

ARCH="$(go env GOARCH)" \
OS="$(go env GOOS)" \
VERSION="$(echo "${IMAGE_VERSION}" | sed -E 's/-[0-9]+$//')" \
BUILD_DEBUG="0" \
./build/build.sh
EOF

FROM debian:bookworm-slim

SHELL ["/bin/bash", "-c"]

RUN <<EOF
set -x
set -e
set -o pipefail

DEBIAN_FRONTEND=noninteractive apt-get update
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    bash \
    coreutils \
    git \
    openssh-client \
    ca-certificates \
    curl \
    socat
EOF

RUN <<EOF
set -x
set -e
set -o pipefail

echo "git-sync:x:65533:65533::/git:/sbin/nologin" >> /etc/passwd
mkdir -m 02775 /git && chown 65533:65533 /git
EOF

COPY --from=builder /go/bin/git-sync /usr/local/bin/git-sync

WORKDIR /git

USER 65533:65533

ENV GITSYNC_ROOT=/git

ENTRYPOINT ["/usr/local/bin/git-sync"]
