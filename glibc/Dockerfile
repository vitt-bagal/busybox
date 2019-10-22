ARG ARCH=""
FROM ${ARCH}debian:stretch-slim

COPY /usr/bin/qemu* /usr/bin/
#RUN if [ "${ARCH}" = "s390x" ] ; then COPY /usr/bin/qemu-s390x-static:/usr/bin/qemu-s390x-static ; fi 

RUN \
    apt-get update && apt-get install -y --no-install-recommends \
      ca-certificates \
      gcc \
      netbase \
    && rm -rf /var/lib/apt/lists/*

RUN set -x \
    && mkdir -p rootfs/lib \
    && set -- \
        /etc/nsswitch.conf \
        /etc/ssl/certs/ca-certificates.crt \
        /usr/share/zoneinfo \
        /etc/services \
        /lib/"$(gcc -print-multiarch)"/libpthread.so.* \
    && while [ "$#" -gt 0  ]; do \
        f="$1"; shift; \
        fn="$(basename "$f")"; \
        if [ -e "rootfs/lib/$fn" ]; then continue; fi; \
        if [ "${f#/lib/}" != "$f" ]; then \
            ln -vL "$f" "rootfs/lib/$fn"; \
        else \
            d="$(dirname $f)" \
            && mkdir -p "rootfs/${d#/}" \
            && cp -av "$f" "rootfs/${f#/}"; \
        fi; \
    done

FROM        ${ARCH}busybox:glibc
MAINTAINER  The Prometheus Authors <prometheus-developers@googlegroups.com>

COPY --from=0 /rootfs /
