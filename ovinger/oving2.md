# Øving 2

I denne øvingen skal vi faktisk legge til våre egne data og sette opp konfigurasjonsfilen for pygeoapi.
Vi har valgt å bruke datasettet 'Administrative enheter" som kan lastes ned på geonorge.

Her hjelper vi dere litt..
Vi har lastet ned datasettet som en "postgis dump" fra geonorge.no lagt til oppsett for å flytte dataene inn i en postgis database. Pygeoapi kan bruke diverse filer som kildedata (eks. esri fgdb), men som oftest benytter man en databaseserver.

Dataene og opsettet for å få disse inn i en docker container ligger under mappen 'assets/postgis'.

Gå videre til neste steg for å ta det i bruk.

## 2.1 Utvid docker-compose filen med vår egen database

Vi skal nå kjøre opp vår egen postgis database i en docker container. Vi skal også spesifisere at pygeoapi skal ha muligheten til å koble seg til denne. Det går i to steg.

Steg 1, definer database service.  
Begynn med å lime inn følgende tekst helt nederst i docker compose filen:

```yml
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

**Obs!** Her er det viktig å passe på mellomrom før "postgis" etter innliming. Det skal være like mange mellomrom foran denne som foran "pygeoapi" (som står nest øverst i filen)

Steg 2:
Utvid pygeoapi med "environment" som inneholder oppkoblingsparametere til databasen.
Kan f.eks. limes inn rett under linjen "restart: unless-stopped"

```yml
environment:
  - DB_NAME=administrative_enheter
  - POSTGRES_USER=postgres
  - POSTGRES_PASSWORD=qwer1234
  - POSTGRES_HOST=postgis
  - POSTGRES_DB=administrative_enheter
```

Dette er miljøvariabler som vi gir til pygeoapi-containeren. De inneholder oppkoblingsparametere til databasen og fanges automatisk opp av pygeoapi.
(Om dere senere setter opp noe lignende med en "ekte" database; bruk en .env fil som _ikke_ committes til github. Eksemelet vårt over er egentlig litt fyfy)

## 2.2 Kjør docker compose up -d på nytt

```
docker compose up -d
```

Klikk deg gjerne litt rundt.

Finner du fylker og kommuner under [collections](http://localhost:5000/collections?f=html)?
I så fall er du flinkere enn oss. 🙂
Vi har gitt pygeoapi-containeren _tilgang_ til databasen, men vi har enda ikke bedt den om å bruke disse dataene.
Dette må vi gjøre med en pygeoapi konfigurasjonsfil.

Gå til [neste øving](oving3.md) så ser vi hvordan vi får pygeoapi til å faktisk bruke disse dataene.

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
    # For mac
    platform: linux/amd64
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

</details>
