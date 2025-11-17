FROM postgis/postgis:17-3.6-alpine

COPY ./data/* /tmp/data/
COPY initdb.sh /docker-entrypoint-initdb.d/initdb.sh

RUN apk --update add unzip \
    && cd /tmp/data \
    && unzip -p fylker.zip > fylker.sql \
    && unzip -p kommuner.zip > kommuner.sql \
    && rm *.zip \
    && chown -R postgres:postgres /tmp/data \
    && chmod +x /docker-entrypoint-initdb.d/initdb.sh

EXPOSE 5432
