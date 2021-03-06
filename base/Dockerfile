FROM ubuntu:20.04

ENV  WINEARCH=win64 \
    WINEDEBUG=-all,err \ 
    DISPLAY=:0.0 \
    WINE_MONO_VERSION=5.0.0 \
    WINETRICKS_VERSION=20200412

RUN export DEBIAN_FRONTEND="noninteractive" \
    && echo "Configuring Ubuntu mirrors with German country code and required tools for Wine" \
    && sed -i -e 's/http:\/\/archive/http:\/\/de.archive/g' /etc/apt/sources.list \
    && apt-get update \
    && apt-get install -y \
        gnupg2 \
        ca-certificates \
        curl \
        p7zip \
        unzip \
        xz-utils \
        zenity \
        cabextract \
        binutils \
        winbind \
    && echo "Configuring WineHQ mirror" \
    && dpkg --add-architecture i386 \
    && curl -L https://dl.winehq.org/wine-builds/winehq.key -o /etc/apt/winehq.key \
    && apt-key add /etc/apt/winehq.key \
    && echo "deb https://dl.winehq.org/wine-builds/ubuntu/ eoan main" >> /etc/apt/sources.list \
    && apt-get update \
    && apt-get install -y --install-recommends \
        winehq-staging \
    && echo "Download winetricks" \
    && curl -L https://raw.githubusercontent.com/Winetricks/winetricks/${WINETRICKS_VERSION}/src/winetricks -o /usr/local/bin/winetricks \
    && chmod +rx /usr/local/bin/winetricks \
    && echo "Installing wine-mono" \
    && curl -L https://dl.winehq.org/wine/wine-mono/${WINE_MONO_VERSION}/wine-mono-${WINE_MONO_VERSION}-x86.tar.xz  -o /tmp/mono.tar.xz \
    && mkdir -p /usr/share/wine/mono/ \
    && tar -xf /tmp/mono.tar.xz -C /usr/share/wine/mono/ \
    && echo "Cleaning system" \
    && apt-get autoclean \
    && apt-get clean \
    && apt-get autoremove --purge \
    && rm -rf /var/apt/cache/* \
    && rm -rf /tmp/*  \
    && echo "Creating wine user" \
    && mkdir -p /home/wine \
    && useradd --system -d /home/wine --shell /bin/bash --uid 1000 --gid root wine \
    && chown -R wine /home/wine

USER wine

WORKDIR /home/wine

RUN echo "Initializing the wine environment for the current user" \
    && wine wineboot --init \
    && while pgrep wineserver > /dev/null; do sleep 1; done \
    && winetricks --unattended win10 \
    && echo "Deleting cache files" \
    && rm -rf /home/wine/.cache/*

ENTRYPOINT [ "/bin/bash", "-c", "wine64 cmd" ]