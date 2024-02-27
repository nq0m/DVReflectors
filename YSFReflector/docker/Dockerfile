FROM debian:bookworm-slim AS builder

RUN apt-get update && \
    apt-get -y upgrade && \
    apt-get install -y build-essential git

RUN mkdir /code && \
    git clone https://github.com/nq0m/DVReflectors.git /code && \
    cd /code/YSFReflector/ && \
    make clean all && \
    mkdir /dashboard && \
    cd /dashboard && \
    git clone https://github.com/dg9vh/YSFReflector-Dashboard.git /dashboard

FROM debian:bookworm-slim

ENV REFLECTOR_NAME set_me
ENV REFLECTOR_DESCRIPTION set_me

RUN apt-get update && \
    apt-get -y upgrade && \
    apt-get install -y procps apache2 libapache2-mod-php php-cgi php-common php && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    mkdir /app

COPY --from=builder /code/YSFReflector/YSFReflector.ini /code/YSFReflector/YSFReflector /app/
COPY --from=builder /dashboard/favicon.ico /dashboard/index.php /dashboard/txinfo.php /dashboard/version.php /var/www/htmo
COPY --from=builder /dashboard/include /var/www/html/include
COPY entrypoint.sh /entrypoint.sh
copy config.php /var/www/html/config

EXPOSE 80/tcp
EXPOSE 42000/udp

ENTRYPOINT ["/entrypoint.sh"]
CMD ["/app/YSFReflector", "/app/YSFReflector.ini"]
HEALTHCHECK CMD ps aux | grep [Y]SFReflector || exit 1
