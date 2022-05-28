# syntax=docker/dockerfile:1
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y munin-node && \
    mkdir -p /var/run/munin # create directory for munin-node.pid file

COPY munin-node.conf /etc/munin/munin-node.conf
CMD munin-node && tail -F /dev/null
