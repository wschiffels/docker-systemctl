FROM debian:buster

RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends \
       init \
       python3.7 \
       python \
       ca-certificates \
       procps \
       sudo \
       curl \
       sysvinit-utils \
       systemd \
       systemd-sysv \
       runit \
    && rm -Rf /var/lib/apt/lists/* \
    && rm -Rf /usr/share/doc && rm -Rf /usr/share/man \
    && apt-get clean \
    && cp /bin/true /sbin/agetty

    # docker-systemctl-replacement
RUN curl https://cdn.jsdelivr.net/gh/gdraheim/docker-systemctl-replacement@1.4.4181/files/docker/systemctl.py -o /bin/systemctl \
    && chmod +x /bin/systemctl

RUN (cd /lib/systemd/system/sysinit.target.wants/; ls | grep -v systemd-tmpfiles-setup.service | xargs rm -f $1); \
    rm -f /lib/systemd/system/multi-user.target.wants/*; \
    rm -f /etc/systemd/system/*.wants/*; \
    rm -f /lib/systemd/system/local-fs.target.wants/*; \
    rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
    rm -f /lib/systemd/system/basic.target.wants/*; \
    rm -f /lib/systemd/system/anaconda.target.wants/*;

RUN systemctl set-default multi-user.target || true

CMD [ "/bin/systemctl" ]
