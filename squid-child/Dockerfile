FROM centos:latest
MAINTAINER tyage <namatyage@gmail.com>

RUN set -x && \
    yum update -y && \
    yum install -y squid openssl && \
    mkdir /etc/squid/ssl && \
    cd /etc/squid/ssl && \
    openssl req -new -newkey rsa:2048 -sha256 -days 3650 -nodes -x509 -keyout squid.pem -out squid.pem && \
    openssl x509 -in squid.pem -outform DER -out squid.der

COPY squid.conf /etc/squid/squid.conf

CMD ["squid"]
