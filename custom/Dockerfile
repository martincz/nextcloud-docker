FROM nextcloud:33.0.10-apache

RUN set -ex; \
    \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        vim \
        sudo \
        ssl-cert \
        certbot \
        python3-certbot-apache \
        ffmpeg \
        ghostscript \
        libmagickcore-7.q16-10-extra \
        imagemagick \
        procps \
        smbclient \
        supervisor \
    ; \
    rm -rf /var/lib/apt/lists/*

RUN set -ex; \
    \
    savedAptMark="$(apt-mark showmanual)"; \
    \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        libbz2-dev \
#        libc-client-dev \ # Throws error with Trixie
        libkrb5-dev \
        libsmbclient-dev \
    ;

# # # Workaround: libc-client-dev is installed from buster image
# Add Buster repository for libc-client-dev only
RUN echo "deb [signed-by=/usr/share/keyrings/debian-archive-keyring.gpg] http://archive.debian.org/debian/ buster main" > /etc/apt/sources.list.d/buster.list

# Install libc-client-dev package from Buster repository
RUN apt-get update && apt-get install -y libc-client-dev

# Clean up the added repository and pin file
RUN rm /etc/apt/sources.list.d/buster.list && apt-get update;

RUN set -ex; \
    docker-php-ext-install bz2; \
    pecl install --configureoptions 'with-kerberos="yes" with-imap-ssl="yes"' imap-1.0.3; \
    docker-php-ext-enable imap; \
    pecl install smbclient; \
    docker-php-ext-enable smbclient; \
    rm -rf /var/lib/apt/lists/*

RUN a2enmod ssl; \
    a2ensite default-ssl

RUN mkdir -p \
    /var/log/supervisord \
    /var/run/supervisord \
;

COPY supervisord.conf /

ENV NEXTCLOUD_UPDATE=1

CMD ["/usr/bin/supervisord", "-c", "/supervisord.conf"]
