# syntax=docker/dockerfile:1

FROM debian:trixie

ARG SALT_VERSION=3007.6

RUN <<EOF
set -x
set -e
apt-get update
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    dumb-init \
    python3 \
    wget \
    ca-certificates \
;

apt-get clean
rm -rf /var/lib/apt/lists/*

EOF

ENTRYPOINT ["/usr/bin/dumb-init"]
CMD ["salt-master"]
EXPOSE 4505 4506 8000
VOLUME /etc/salt/pki/

RUN <<EOF
set -x
set -e

wget -O /tmp/bootstrap-salt.sh https://github.com/saltstack/salt-bootstrap/releases/download/v2025.02.24/bootstrap-salt.sh
chmod +x /tmp/bootstrap-salt.sh
/tmp/bootstrap-salt.sh \
    -N \
    -M \
    -W \
    -L \
    -d \
    -F \
    -X \
    stable \
    "${SALT_VERSION}" \
;
rm /tmp/bootstrap-salt.sh

EOF

ARG DBG=false
RUN <<EOF
if [ "${DBG}" == "true" ]
then
    pip3 install \
    --no-build-isolation \
    --no-cache-dir \
    debugpy \
;
fi
EOF
