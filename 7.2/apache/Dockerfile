# Prove that the Debian how-to works, please choose one of the 3 installation methods
# and optionally save the resulting extension: sudo cp /usr/lib/php/20151012/pdo_sqlsrv.so /opt/results/

FROM php:7.2.5-apache

ENV LC_ALL C
ENV DEBIAN_FRONTEND noninteractive

##USER debian
#RUN mkdir /home/debian
#RUN cd /home/debian
#VOLUME ["/opt/results"]

RUN apt-get update && apt-get -y install debhelper  devscripts \
dh-exec dh-autoreconf libreadline-dev libltdl-dev wget apt-utils firebird-dev apt-transport-https nano gdb apt-utils
#\ libaio1 libaio-dev

#RUN echo "deb http://packages.dotdeb.org stretch all" \
#    |  tee /etc/apt/sources.list.d/dotdeb.list \
#    && wget -qO- https://www.dotdeb.org/dotdeb.gpg \
#    |  apt-key add - \
#    &&  apt-get update \
#    &&  apt-get upgrade -qq

RUN apt-get update && apt-get -y install locales
RUN  echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    echo "ru_RU ISO-8859-5" >> /etc/locale.gen && \
    echo "ru_RU.KOI8-R KOI8-R" >> /etc/locale.gen && \
    echo "ru_RU.UTF-8 UTF-8" >> /etc/locale.gen && \
    echo "ru_UA KOI8-U" >> /etc/locale.gen && \
    echo "ru_UA.UTF-8 UTF-8" >> /etc/locale.gen && \
    dpkg-reconfigure locales

RUN curl https://packages.microsoft.com/config/debian/9/prod.list > /etc/apt/sources.list.d/mssql-release.list \
    &&  apt-key update && apt-get update \
    &&  ACCEPT_EULA=Y apt-get  install -y --force-yes msodbcsql17 \
    && apt-get update &&  ACCEPT_EULA=Y apt-get install -y  --force-yes  mssql-tools \
    && echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc \
    && apt-get install -y unixodbc-dev
# Fake uname for install.sh
RUN printf '#!/bin/bash\nif [ "$*" == "-p" ]; then echo "x86_64"; else /bin/uname "$@"; fi' \
    |  tee /usr/local/bin/uname \
    &&  chmod +x /usr/local/bin/uname

RUN mkdir  /opt/oracle \
    && cd /opt/oracle \
    && wget -q http://sync.helpme24.ru/instantclient-basic-linux.x64-12.2.0.1.0.zip \
    && wget -q http://sync.helpme24.ru/instantclient-sdk-linux.x64-12.2.0.1.0.zip \
    && unzip /opt/oracle/instantclient-basic-linux.x64-12.2.0.1.0.zip -d /opt/oracle \
    && unzip /opt/oracle/instantclient-sdk-linux.x64-12.2.0.1.0.zip -d /opt/oracle \
    && ln -s /opt/oracle/instantclient_12_2/libclntsh.so.12.1 /opt/oracle/instantclient_12_2/libclntsh.so \
    && ln -s /opt/oracle/instantclient_12_2/libclntshcore.so.12.1 /opt/oracle/instantclient_12_2/libclntshcore.so \
    && ln -s /opt/oracle/instantclient_12_2/libocci.so.12.1 /opt/oracle/instantclient_12_2/libocci.so \
    && rm -rf /opt/oracle/*.zip

WORKDIR /opt/oracle/
RUN wget -q https://pecl.php.net/get/oci8-2.1.8.tgz
 RUN       tar -xzf oci8-2.1.8.tgz
        WORKDIR   /opt/oracle/oci8-2.1.8
        RUN phpize
       RUN ./configure --with-oci8=instantclient,/opt/oracle/instantclient_12_2
       RUN make install
RUN echo "/opt/oracle/instantclient_12_2" >> /etc/ld.so.conf.d/oracle-instantclient.conf
RUN echo 'export LD_LIBRARY_PATH="/opt/oracle/instantclient_12_2"' >> ~/.bashrc
RUN echo 'export ORACLE_HOME="/opt/oracle/instantclient_12_2"' >> ~/.bashrc
RUN rm -rf /opt/oracle/oci8-2.1.8
RUN rm -rf /opt/oracle/oci8-2.1.8.tgz

#RUN  rm -rf /root/* && \
#        rm -rf /tmp/*
#RUN apt-get update
#RUN apt-get install -y \
#    curl apt-utils apt-transport-https debconf-utils gcc build-essential libc6 g++\
#    && rm -rf /var/lib/apt/lists/*

# adding custom MS repository
#RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
#RUN curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list

# install SQL Server drivers
#RUN apt-get update && ACCEPT_EULA=Y apt-get install -y unixodbc-dev msodbcsql

# install SQL Server tools
#RUN apt-get update && ACCEPT_EULA=Y apt-get install -y mssql-tools
#RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
#RUN /bin/bash -c "source ~/.bashrc"
#RUN apt-get purge -y --auto-remove devscripts debhelper dh-exec dh-autoreconf locales wget apt-utils

