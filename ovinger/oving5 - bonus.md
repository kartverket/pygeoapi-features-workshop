# Øving 5 – Bonus: Eksperimenter med Templating i pygeoapi

I denne øvingen skal du utforske hvordan pygeoapi kan tilpasses med egne maler (templates) og statiske filer. Dette gir deg mulighet til å endre utseende og funksjonalitet på API-et, for eksempel for å tilpasse HTML-visningen eller legge til egne bilder og CSS.


## 5.1 Rediger docker-compose.yml og config.yml

For å legge til templates som du selv kan redigere, mounter vi config-filen inn som tidligere, men i tillegg har vi `static/` og `templates/` mappene som skal mountes:

```
    volumes:
      - ./config/pygeoapi_config.yml:/pygeoapi/local.config.yml
      - ./assets/oving5 - templating/templates:/pygeoapi/templates # sti til egne maler
      - ./assets/oving5 - templating/static:/pygeoapi/static # sti til egne statiske filer
```

I config.yml må vi også peke på hvor malen vår og filene ligger. Dette legges under server-blokka i yaml-filen: 

```
server:
  ...
  logo: http://localhost:5000/static/img/organization_logo.png # sti til logo, må være en URL
  templates:
    path: /pygeoapi/templates # sti til maler
    static: /pygeoapi/static # sti til statiske filer
```

Start opp løsning som tidligere med 
``` docker compose up -d``` 

## 5.2 Eksperimenter med templating (landing page, CSS og logo)

1. Åpne pygeoapi i nettleseren: [http://localhost:5000](http://localhost:5000)
2. Naviger til forsiden (landing page).
3. Se hvordan HTML-visningen ser ut.

### Oppgave:

`templates/` og `static/` ligger under `assets/`-mappen.
- Endre på CSS i `static/css/default.css` for å tilpasse utseendet.
- Bytt ut `organization_logo.png` under `static/img/` med din egen organisasjonslogo (men behold filnavnet).
- Gå inn i `templates/`-mappen i løsningen. Finn filen `landing_page.html`.
  - Kan vi for eksempel flytte info om lisens og de andre lenkene til en egen boks under Contact Point?
    ```
        <div class="card">
          <div class="card-header">
              <b>{% trans %}Legal/Misc.{% endtrans %}</b>
          </div>
          <div class="card-body">
            <div>
              <b>{% trans %}License{% endtrans %}</b><br/>
              <span><a href="{{ config['metadata']['license']['url'] }}">{{ config['metadata']['license']['name'] }}</a></span><br/>
              <b>{% trans %}Terms of Service{% endtrans %}</b><br/>
              <span><a href="{{ config['metadata']['identification']['terms_of_service'] }}">{{ config['metadata']['identification']['terms_of_service'] }}</a></span><br/>
              <b>{% trans %}URL{% endtrans %}</b><br/>
              <span><a href="{{ config['metadata']['identification']['url'] }}">{{ config['metadata']['identification']['url'] }}</a></span><br/>
            </div>
          </div>
        </div>
    ```
  - Får du fjernet den opprinnelige boksen også?
  - Ser du andre endringer du vil gjøre, f.eks. legge til en tekst, endre overskrift, eller sett inn et bilde/logo fra `static/img/`?.
- Lagre filene og last inn siden på nytt i nettleseren. (Du må kanskje restarte pygeoapi-containeren: `docker compose up -d`)


## 5.3 Oppsummering

Med templating og statiske filer kan du tilpasse pygeoapi til å passe din organisasjon eller ditt prosjekt. Prøv deg frem og se hvor mye du kan endre på utseendet!

> 💡 Husk: Endringer i maler og statiske filer krever ofte restart av pygeoapi-containeren for å tre i kraft.

<details>
<summary>Fasit</summary>
Filen docker-compose.yml skal etter denne øvingen se slik ut:

```yml
services:
  pygeoapi:
    image: geopython/pygeoapi:latest
    container_name: pygeoapi_ws
    ports:
      - "5000:80"
    volumes:
     - ./config/pygeoapi_config.yml:/pygeoapi/local.config.yml
     - ./assets/oving5 - templating/templates:/pygeoapi/templates # sti til egne maler
     - ./assets/oving5 - templating/static:/pygeoapi/static # sti til egne statiske filer (bilder/ikoner, styling etc.)
    restart: unless-stopped
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
      context: ./assets/postgis
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
  templates:
    path: /pygeoapi/templates # sti til maler
    static: /pygeoapi/static # sti til statiske filer
  logo: http://localhost:5000/static/img/organization_logo.png # sti til logo

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

---

**Ressurser for videre lesning:**
- [pygeoapi docs: Customizing templates](https://docs.pygeoapi.io/en/latest/html-templating.html)
- [Jinja template syntax](https://jinja.palletsprojects.com/en/stable/templates/)
