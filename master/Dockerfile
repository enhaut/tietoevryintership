# syntax=docker/dockerfile:1
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y apache2 munin cron && \
    mkdir -p /var/run/munin # create directory for munin-node.pid file

COPY 000-default.conf /etc/apache2/sites-available/000-default.conf
COPY munin.conf /etc/munin/munin.conf

RUN chown -R munin:munin /var/run/munin && \
    chmod 0644 /etc/cron.d/munin

CMD runuser -u munin -- munin-cron && cron && apachectl start && tail -F /dev/null
