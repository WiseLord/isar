FROM debian:bullseye-slim as isar-docker

ARG TARGETPLATFORM
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y locales && \
    localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

ENV LANG=en_US.utf8
ENV LC_ALL=en_US.UTF-8

# Isar main dependencies
RUN apt-get install -y -f --no-install-recommends \
        binfmt-support bzip2 debootstrap dosfstools dpkg-dev gettext-base \
        git mtools parted python3 quilt qemu-user-static reprepro sudo \
        unzip xz-utils git-buildpackage pristine-tar sbuild schroot zstd \
        python3-distutils \
        umoci skopeo

# Isar testsuite dependencies
RUN apt-get install --no-install-recommends -y \
        python3-pip && \
    pip3 --proxy=$https_proxy install avocado-framework==100.1 && \
    rm -rf $(pip3 cache dir) && \
    apt-get install -y -f --no-install-recommends \
        qemu-system ovmf

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* 

COPY contrib/oe-git-proxy /usr/bin/
ENV GIT_PROXY_COMMAND="oe-git-proxy" \
    NO_PROXY="*"

RUN echo "builder ALL=NOPASSWD: ALL" > /etc/sudoers.d/builder-nopasswd && \
    chmod 660 /etc/sudoers.d/builder-nopasswd && \
    echo "Defaults env_keep += \"ftp_proxy http_proxy https_proxy no_proxy\"" \
    > /etc/sudoers.d/env_keep && chmod 660 /etc/sudoers.d/env_keep

RUN useradd builder --user-group --create-home --home-dir /builder && \
    sbuild-adduser builder >/dev/null 2>/dev/null

COPY container-entrypoint /

USER builder

ENTRYPOINT ["/container-entrypoint"]
