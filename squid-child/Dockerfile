FROM centos:latest
MAINTAINER tyage <namatyage@gmail.com>

RUN set -x && \
    yum update -y && \
    yum install -y squid openssl && \
    mkdir /etc/squid/ssl && \
    cd /etc/squid/ssl && \
    openssl req -new -newkey rsa:2048 -sha256 -days 3650 -nodes -x509 -keyout squid.pem -out squid.pem -subj '/CN=localhost/O=localhost/C=JP' && \
    openssl x509 -in squid.pem -outform DER -out squid.der && \
    /usr/lib64/squid/ssl_crtd -c -s /var/lib/ssl_db && \
    chown -R squid /var/lib/ssl_db && \
    touch /var/log/squid/access.log && \
    chown squid /var/log/squid/access.log

COPY squid.conf /etc/squid/squid.conf

CMD /sbin/squid && tail -10f /var/log/squid/access.log

EXPOSE 3128
