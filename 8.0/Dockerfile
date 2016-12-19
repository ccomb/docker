FROM debian:jessie
MAINTAINER Christophe Combelles. <ccomb@free.fr>

COPY sources.list /etc/apt/

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            node-less \
            node-clean-css \
            python-pyinotify \
            python-renderpm \
            python-support \
        && curl -o wkhtmltox.deb -SL http://nightly.odoo.com/extra/wkhtmltox-0.12.1.2_linux-jessie-amd64.deb \
        && echo '40e8b906de658a2221b15e4e8cd82565a47d7ee8 wkhtmltox.deb' | sha1sum -c - \
        && dpkg --force-depends -i wkhtmltox.deb \
        && apt-get -y install -f --no-install-recommends \
        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb

# Install Odoo
ENV COMMIT fba04cca1e93ea42e471ca6e30735b86c81b1d4e

RUN set -x; \
        apt-get update && \
        apt-get build-dep -y python \
        && apt-get install -y --no-install-recommends \
            adduser \
            antiword \
            ghostscript \
            graphviz \
            libevent-dev \
            libffi-dev \
            libfreetype6-dev \
            libjpeg-dev \
            liblcms2-dev \
            libldap2-dev \
            libpq-dev \
            libsasl2-dev \
            libxml2-dev \
            libxslt1-dev \
            libyaml-dev \
            poppler-utils \
            postgresql-client \
            python \
            python-babel \
            python-dateutil \
            python-decorator \
            python-dev \
            python-docutils \
            python-feedparser \
            python-gevent \
            python-imaging \
            python-jinja2 \
            python-ldap \
            python-libxslt1 \
            python-lxml \
            python-mako \
            python-mock \
            python-openid \
            python-passlib \
            python-pip \
            python-psutil \
            python-psycopg2 \
            python-pychart \
            python-pydot \
            python-pyparsing \
            python-pypdf \
            python-reportlab \
            python-requests \
            python-simplejson \
            python-tz \
            python-unittest2 \
            python-vatnumber \
            python-vobject \
            python-werkzeug \
            python-xlwt \
            python-yaml \
            unzip \
            zlib1g-dev \
        && pip install pip==9.0.1 setuptools==32.1.0 \
        && cd /srv && curl -o odoo.zip -SL https://github.com/anybox/odoo/archive/fba04cca1e93ea42e471ca6e30735b86c81b1d4e.zip \
        && unzip odoo.zip && rm odoo.zip && mv odoo-${COMMIT} odoo && cd odoo \
        && pip install -r requirements.txt \
        && apt-get remove -y \
            binutils \
            bsdmainutils \
            build-essential \
            bzip2 \
            comerr-dev \
            cpp \
            cpp-4.9 \
            debhelper \
            debiandoc-sgml \
            dpkg-dev \
            file \
            g++ \
            g++-4.9 \
            gcc \
            gcc-4.9 \
            gettext \
            gettext-base \
            groff-base \
            intltool-debian \
            krb5-multidev \
            libasan1:amd64 \
            libasprintf0c2:amd64 \
            libatomic1:amd64 \
            libc-dev-bin \
            libc6-dev \
            libcilkrts5:amd64 \
            libcloog-isl4:amd64 \
            libcroco3:amd64 \
            libdpkg-perl \
            libevent-2.0-5:amd64 \
            libevent-core-2.0-5:amd64 \
            libevent-dev \
            libevent-extra-2.0-5:amd64 \
            libevent-openssl-2.0-5:amd64 \
            libevent-pthreads-2.0-5:amd64 \
            libexpat1-dev \
            libffi-dev \
            libfreetype6-dev \
            libgcc-4.9-dev \
            libgomp1:amd64 \
            libgssrpc4:amd64 \
            libhtml-tagset-perl \
            libisl10:amd64 \
            libitm1:amd64 \
            libjpeg-dev \
            libjpeg62-turbo-dev \
            libkadm5clnt-mit9:amd64 \
            libkadm5srv-mit9:amd64 \
            libkdb5-7:amd64 \
            liblcms2-dev \
            libldap2-dev \
            liblsan0:amd64 \
            libmagic1:amd64 \
            libmpc3:amd64 \
            libmpfr4:amd64 \
            libpipeline1:amd64 \
            libpq-dev \
            libpython-dev \
            libpython2.7-dev \
            libquadmath0:amd64 \
            libsasl2-dev \
            libsp1c2 \
            libstdc++-4.9-dev \
            libtext-format-perl \
            libtimedate-perl \
            libtsan0:amd64 \
            libubsan0:amd64 \
            libunistring0:amd64 \
            liburi-perl \
            libxml2-dev \
            libxslt1-dev \
            libyaml-dev \
            linux-libc-dev \
            lsb-release \
            make \
            man-db \
            patch \
            perl \
            perl-modules \
            po-debconf \
            python-all \
            python-dev \
            python-pip \
            python2.7-dev \
            sgml-data \
            sp \
            unzip \
            xz-utils \
            zlib1g-dev



# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/
RUN adduser --system --quiet odoo \
    && chown odoo: /etc/odoo/odoo.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons /var/lib/odoo \
        && chown -R odoo: /var/lib/odoo /mnt/extra-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV OPENERP_SERVER /etc/odoo/odoo.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["/srv/odoo/odoo.py"]
