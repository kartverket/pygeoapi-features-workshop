# Øving 3

Pygeoapi trenger å vite hvor den skal lete etter data. I forrige øving kjørte vi opp en database, men vi "glemte" å konfiguere pygeoapi til å bruke disse dataene. Det skal vi fikse nå.

## 3.1 Benytt egen konfigurasjonsfil for pygeoapi

Konfigurasjonsfilen styrer en del ting som metadata og koblig til datakilde.
Det blir fort litt mye å fylle ut og vi har derfor laget en ferdig utfyllt config-fil i mappen "config".
Ta gjerne en titt i filen og spesielt det som er under "resources". Her spesifiserer vi datasettene våre, som i dette tilfellet er kommuner og fylker.

For å ta denne configfilen i bruk, så benytter vi docker volume mount og mounter config-fila inn i pygeoapi-containeren på container-stien: /pygeoapi/local.pygeoapi_config.yml
Dersom denne filen eksisterer på angitt sti i containeren vil den ta presedens over standard configfil.

Gå til docker-compose filen og lim inn følgende for å spesifisere at configfilen skal mountes inn som et volum:
(Det skal limes inn under pygeoapi tjenesten i docker-compose.yml. Eks. under "ports" seksjonen.)

```yml
volumes:
  - ./config/pygeoapi_config.yml:/pygeoapi/local.config.yml # Her spesifiserer vi at filen pygeoapi_config.yml i denne mappen skal importeres inn i containeren
```

<details>
<summary>Eksempel</summary>

```yml
...
        pygeoapi:
          image: geopython/pygeoapi:latest
          container_name: pygeoapi_ws
          ports:
            - "5000:80"
Her -->   volumes:
            - ./config/pygeoapi_config.yml:/pygeoapi/local.pygeoapi_config.yml
          restart: unless-stopped
          environment:

...
```

</details>

Kjør så kommandoen

```
docker compose up -d
```

for å starte på nytt med endringene vi har gjort.

Når disse to linjene er lagt inn vil det være en koblig mellom filen pygeoapi_config.yml i dette workspacet og filen local.config.yml som lever inne i pygeoapi containeren.
Du kan derfor redigere filen pygeoapi_config.yml som du vil og pygeoapi får meg seg endringene. Du er imidlertidig nødt til å restarte pygeoapi hver gang du gjør endringer i filen, da pygeoapi leser denne filen inn ved oppstart. (Kan endres til "hot reloading" ved eks. å overstyre entrypoint kommandoen til pygeoapi imaget)

For videre endringer i filen _pygeoapi_config.yml_ så holder det å skrive `docker compose restart pygeoapi_ws` for å restarte pygeoapi med oppdatert config. Men om det gjøres endringer i filen docker-compose.yaml, så må "docker compose up -d" kjøres.

Du kan nå åpne åpne `localhost:5000` i nettleseren igjen og se om det har skjedd noe.

> 💡 **Tips:** Inspiser docker desktop eller skriv kommandoen `docker ps` for oversikt over kjørende containere.
> Vi skal nå ha 2 kjørende containere, 1 for pygeoapi og 1 for databasen vår.

## 3.2 Finn en feil og fiks konfigurasjonsfilen

Vi har selvfølgelig klart å gjøre en feil i oppsettet vårt. Klarer du å finne feilen(e)?

<details>
<summary>Hint</summary>
Fylker peker mot kommuner og kommuner peker mot fylker. Det er kansjke ikke så lurt.
Kan sees om man eks. går til ```http://localhost:5000/collections/fylker/items```
Dette er feil som må rettes opp i konfigurasjonsfilen pygeoapi_config.yml. 
</details>

Fiks feilene i filen og kjør:
`docker compose restart pygeoapi_ws`
ev.
`docker compose up -d` (gjør samme nytten, men litt mer også)

<details>
<summary>Fasit</summary>
Filen docker-compose.yml skal etter denne øvingen se slik ut:

```yml
services:
  pygeoapi:
    image: geopython/pygeoapi:latest # 'Sti' til image. Vi bruker 'latest' versjon her, men det er ofte lurt å spesifisere med versjonsnummer
    container_name: pygeoapi_ws # valgfritt, men det er fint å sette eget container navn
    ports:
      - "5000:80" # Her 'mappes' port 80 i containeren med port 5000 på pc'en din
    volumes:
      - ./config/pygeoapi_config.yml:/pygeoapi/local.pygeoapi_config.yml # Her spesifiserer vi at filen pygeoapi_config.yml i denne mappen skal importeres inn i containeren
    restart: unless-stopped # Containeren restarter seg selv, med mindre den får en stopp-kommando. Eks. 'docker compose down'
    depends_on:
      postgis:
        condition: service_healthy
    environment:
      - DB_NAME=administrative_enheter
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=qwer1234
      - POSTGRES_HOST=postgis
      - POSTGRES_DB=administrative_enheter

  postgis:
    build:
      context: ./assets/postgis # Sti til postgismappen som inneholder en Dockerfile
    container_name: postgis
    ports:
      - "5432:5432"
    environment:
      - DB_NAME=administrative_enheter
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=qwer1234
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 10
```

Filen pygeoapi_config.yml (i mappen config) skal se slik ut:

```yml
server:
  bind:
    host: 0.0.0.0
    port: 5000
  cors: true
  url: http://localhost:5000
  mimetype: application/json; charset=UTF-8
  encoding: utf-8
  gzip: false
  languages:
    - en
  pretty_print: true
  limits:
    default_items: 15
    max_items: 1000
  map:
    url: https://tile.openstreetmap.org/{z}/{x}/{y}.png
    attribution: '&copy; <a href="https://openstreetmap.org/copyright">OpenStreetMap contributors</a>'
  admin: false

logging:
  level: INFO

metadata:
  identification:
    title: Administrative enheter
    description:
      en: Datasettet inneholder administrative grenser og områder for Norge, inkludert fylker og kommuner.
    keywords:
      - Fylke
      - Kommune
      - Administrativ inndeling
      - Administrative grenser
      - Fylkesgrenser
      - Kommunegrenser
      - Riksgrense
    keywords_type: theme
    terms_of_service: https://www.kartverket.no/geodataarbeid/norge-digitalt/partsinformasjon/avtaler-og-vilkar/norge-digitalt-lisens
    url: https://www.kartverket.no

  license:
    name: Norge digitalt-lisens
    url: https://www.kartverket.no/geodataarbeid/norge-digitalt/partsinformasjon/avtaler-og-vilkar/norge-digitalt-lisens
  provider:
    name: Kartverket
    url: https://www.kartverket.no
  contact:
    name: Kartverket
    email: post@kartverket.no
    url: https://www.kartverket.no

resources:
  fylker:
    type: collection
    title: Fylker
    description: Din kule beskrivelse av fylker
    keywords:
      en:
        - Fylke
        - Fylkesgrense
    extents:
      spatial:
        bbox: [4.626095, 57.977101, 31.125157, 71.188325]
        crs: http://www.opengis.net/def/crs/OGC/1.3/CRS84
    # links:
    #   - type: text/html
    #     rel: related
    #     title: Geonorge link
    #     href: https://kartkatalog.geonorge.no/metadata/administrative-enheter-fylker/6093c8a8-fa80-11e6-bc64-92361f002671
    providers:
      - type: feature
        name: PostgreSQL
        data:
          host: ${POSTGRES_HOST}
          dbname: ${POSTGRES_DB}
          user: ${POSTGRES_USER}
          password: ${POSTGRES_PASSWORD}
          search_path: [administrative_enheter_fylker]
        id_field: objid
        table: fylke
        geom_field: omrade
        crs:
          - http://www.opengis.net/def/crs/OGC/1.3/CRS84
          - http://www.opengis.net/def/crs/EPSG/0/25832
          - http://www.opengis.net/def/crs/EPSG/0/25833
          - http://www.opengis.net/def/crs/EPSG/0/25835
          - http://www.opengis.net/def/crs/EPSG/0/4258
          - http://www.opengis.net/def/crs/EPSG/0/4326
          - http://www.opengis.net/def/crs/EPSG/0/3857
        storage_crs: http://www.opengis.net/def/crs/EPSG/0/25833 # Datene vi har i databasen er lagret i koordinatsystemet EPSG:25833
  kommuner:
    type: collection
    title: Kommuner
    description: Din kule beskrivelse av kommuner
    keywords:
      en:
        - Kommune
        - Kommunegrenser
    extents:
      spatial:
        bbox: [4.626095, 57.977101, 31.125157, 71.188325]
        crs: http://www.opengis.net/def/crs/OGC/1.3/CRS84
    # links:
    #   - type: text/html
    #     rel: related
    #     title: Geonorge link
    #     href: https://kartkatalog.geonorge.no/metadata/administrative-enheter-kommuner/041f1e6e-bdbc-4091-b48f-8a5990f3cc5b
    providers:
      - type: feature
        name: PostgreSQL
        data:
          host: ${POSTGRES_HOST}
          dbname: ${POSTGRES_DB}
          user: ${POSTGRES_USER}
          password: ${POSTGRES_PASSWORD}
          search_path: [administrative_enheter_kommuner]
        id_field: objid
        table: kommune
        geom_field: omrade
        crs:
          - http://www.opengis.net/def/crs/OGC/1.3/CRS84
          - http://www.opengis.net/def/crs/EPSG/0/25832
          - http://www.opengis.net/def/crs/EPSG/0/25833
          - http://www.opengis.net/def/crs/EPSG/0/25835
          - http://www.opengis.net/def/crs/EPSG/0/4258
          - http://www.opengis.net/def/crs/EPSG/0/4326
          - http://www.opengis.net/def/crs/EPSG/0/3857
        storage_crs: http://www.opengis.net/def/crs/EPSG/0/25833 # Datene vi har i databasen er lagret i koordinatsystemet EPSG:25833
```

</details>
