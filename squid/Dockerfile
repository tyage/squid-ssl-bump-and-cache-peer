FROM centos:latest
MAINTAINER tyage <namatyage@gmail.com>

RUN set -x && \
    yum update -y && \
    yum install -y squid && \
    touch /var/log/squid/access.log && \
    chown squid /var/log/squid/access.log

COPY squid.conf /etc/squid/squid.conf

CMD /sbin/squid && tail -10f /var/log/squid/access.log

EXPOSE 3128 3129
