# Prove that the Debian how-to works, please choose one of the 3 installation methods
# and optionally save the resulting extension: sudo cp /usr/lib/php/20151012/pdo_sqlsrv.so /opt/results/

FROM php:7.0-apache

ENV LC_ALL C
ENV DEBIAN_FRONTEND noninteractive

##USER debian
#RUN mkdir /home/debian
#RUN cd /home/debian
#VOLUME ["/opt/results"]

RUN apt-get update && apt-get -y install devscripts debhelper dh-exec dh-autoreconf libreadline-dev libltdl-dev wget apt-utils
# Add PHP 7 repository
# for Debian jessie
# And System upgrade

#RUN echo "deb http://packages.dotdeb.org jessie all" \
#    |  tee /etc/apt/sources.list.d/dotdeb.list \
#    && wget -qO- https://www.dotdeb.org/dotdeb.gpg \
#    |  apt-key add - \
#    &&  apt-get update \
#    &&  apt-get upgrade -qq

# Install UnixODBC
# Compile odbc_config as it is not part of unixodbc package
RUN  apt-get install -y whiptail \
    unixodbc libgss3 odbcinst \ #devscripts debhelper dh-exec dh-autoreconf libreadline-dev libltdl-dev \
    && dget -u -x http://http.debian.net/debian/pool/main/u/unixodbc/unixodbc_2.3.1-3.dsc \
    && cd unixodbc-*/ \
    && dpkg-buildpackage -uc -us -B \
    &&  cp -v ./exe/odbc_config /usr/local/bin/

# Fake uname for install.sh
RUN printf '#!/bin/bash\nif [ "$*" == "-p" ]; then echo "x86_64"; else /bin/uname "$@"; fi' \
    |  tee /usr/local/bin/uname \
    &&  chmod +x /usr/local/bin/uname

# Microsoft ODBC Driver 13 for Linux
# Note: There's a copy of this tar on my hubiC
RUN wget -nv -O msodbcsql-13.0.0.0.tar.gz \
    "https://meetsstorenew.blob.core.windows.net/contianerhd/Ubuntu%2013.0%20Tar/msodbcsql-13.0.0.0.tar.gz?st=2016-10-18T17%3A29%3A00Z&se=2022-10-19T17%3A29%3A00Z&sp=rl&sv=2015-04-05&sr=b&sig=cDwPfrouVeIQf0vi%2BnKt%2BzX8Z8caIYvRCmicDL5oknY%3D" \
    && tar -xf msodbcsql-13.0.0.0.tar.gz \
    && cd msodbcsql-*/ \
    && ldd lib64/libmsodbcsql-13.0.so.0.0 \
    &&  ./install.sh install --accept-license \
    && ls -l /opt/microsoft/msodbcsql/ \
    && odbcinst -q -d -n "ODBC Driver 13 for SQL Server"



# (2) Or Build PDO_sqlsrv driver with phpize
RUN  apt-get install -y unixodbc-dev php7.0-dev \
    && cd \
    && wget -nv "https://github.com/Microsoft/msphpsql/archive/PHP-7.0-Linux.tar.gz" \
    && tar -xf PHP-7.0-Linux.tar.gz \
    && cd msphpsql-PHP-7.0-Linux/source/ \
    && cp -r shared/ pdo_sqlsrv/ \
    && cd pdo_sqlsrv/ \
    && phpize \
    && ./configure CXXFLAGS=-std=c++11 \
    && make \
    &&  make "INSTALL=$(pwd)/build/shtool install -c --mode=0644" install
# Build sqlsrv driver with phpize
RUN     cd \
        && cd msphpsql-PHP-7.0-Linux/source/ \
        && cp -r shared/ sqlsrv/ \
        && cd sqlsrv/ \
        && phpize \
        && ./configure CXXFLAGS=-std=c++11 \
        && make \
        &&  make "INSTALL=$(pwd)/build/shtool install -c --mode=0644" install

# Build oracle
RUN mkdir /opt/oracle \
    && cd /opt/oracle \
    && wget -q https://s3.amazonaws.com/merofile/instantclient-basic-linux.x64-12.1.0.2.0.zip \
    && wget -q https://s3.amazonaws.com/merofile/instantclient-sdk-linux.x64-12.1.0.2.0.zip \
    && unzip /opt/oracle/instantclient-basic-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
    && unzip /opt/oracle/instantclient-sdk-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
    && ln -s /opt/oracle/instantclient_12_1/libclntsh.so.12.1 /opt/oracle/instantclient_12_1/libclntsh.so \
    && ln -s /opt/oracle/instantclient_12_1/libclntshcore.so.12.1 /opt/oracle/instantclient_12_1/libclntshcore.so \
    && ln -s /opt/oracle/instantclient_12_1/libocci.so.12.1 /opt/oracle/instantclient_12_1/libocci.so \
    && rm -rf /opt/oracle/*.zip

RUN apt-get update && apt-get -y install locales
RUN  echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    echo "ru_RU ISO-8859-5" >> /etc/locale.gen && \
    echo "ru_RU.KOI8-R KOI8-R" >> /etc/locale.gen && \
    echo "ru_RU.UTF-8 UTF-8" >> /etc/locale.gen && \
    echo "ru_UA KOI8-U" >> /etc/locale.gen && \
    echo "ru_UA.UTF-8 UTF-8" >> /etc/locale.gen && \
    dpkg-reconfigure locales
RUN RUN rm -rf /root/* && \
        rm -rf /tmp/* 

RUN apt-get purge -y --auto-remove devscripts debhelper dh-exec dh-autoreconf locales wget apt-utils

