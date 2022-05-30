# syntax=docker/dockerfile:1
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y munin-node python3 && \
    mkdir -p /var/run/munin # create directory for munin-node.pid file

COPY munin-node.conf /etc/munin/munin-node.conf

COPY tcpconnections /usr/share/munin/plugins/tcpconnections
RUN ln -s /usr/share/munin/plugins/tcpconnections /etc/munin/plugins/tcpconnections && \
    printf "[tcpconnections]\nuser root\n" >> /etc/munin/plugin-conf.d/munin-node

CMD rm -f /var/run/munin/munin-node.pid && munin-node && tail -F /dev/null
