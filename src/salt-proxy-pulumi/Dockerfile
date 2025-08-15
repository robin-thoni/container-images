# syntax=docker/dockerfile:1

ARG SALT_VERSION=3007.6
FROM robinthoni/salt-proxy:${SALT_VERSION}

ARG PULUMI_VERSION=3.188.0

RUN <<EOF
set -x
set -e

curl -fsSL https://get.pulumi.com | sh -s - --version "${PULUMI_VERSION}" --install-root /usr/local --no-edit-path

EOF

RUN <<EOF
set -x
set -e

salt-pip install \
    --no-build-isolation \
    --no-cache-dir \
    pulumi==${PULUMI_VERSION} \
;

EOF
