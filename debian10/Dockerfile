FROM debian:buster
LABEL maintainer="Wolfgang Schiffels"

RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends \
       systemd \
       systemd-cron \
       python3.7 \
       ca-certificates \
       procps \
       sudo \
       init \
    && ln -s /usr/bin/python3.7 /usr/bin/python \
    && rm -Rf /var/lib/apt/lists/* \
    && rm -Rf /usr/share/doc && rm -Rf /usr/share/man \
    && apt-get clean \
    # Disable agetty, fixes zombie agetty 100% cpu.
    # https://github.com/moby/moby/issues/4040
    && cp /bin/true /sbin/agetty

VOLUME [ "/sys/fs/cgroup" ]
CMD [ "/sbin/init" ]
