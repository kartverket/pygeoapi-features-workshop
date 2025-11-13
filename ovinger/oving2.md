# Øving 2
I denne øvingen skal vi faktisk legge til data våre egne data og sette opp konfigurasjonsfilen for pygeoapi. 
Vi har valgt å bruke datasettet 'Administrative enheter" som kan lastes ned på geonorge. Pygeoapi kan bruke diverse filer som kildedata, men som oftest benytter man en databaseserver. 


Her hjelper vi dere litt.. 
Vi har lastet ned datasettet som en "postgis dump" fra geonorge.no lagt til oppsett for å flytte dataene inn i en postgis database. 

Dette opsettet ligger under mappen 'postgis'. Gå videre til neste steg for å ta det i bruk. 


## 1.1 Utvid docker-compose filen
Vi må kjøre opp og spesifisere hvilken database vi skal bruke. Det går i to steg. 

Steg 1, definer databasecontainer:
Begynn med å lime inn følgende tekst helt nederst i docker compose filen:

```  
postgis:
  build:
    context: ./postgis # Sti til postgismappen som inneholder en Dockerfile
  ports:
    - "5432:5432"
  environment:
    - DB_NAME=administrative_enheter
    - POSTGRES_USER=postgres
    - POSTGRES_PASSWORD=qwer1234
```
Obs! Her er det viktig at det ikke er noe mellomrom før "postgis" etter innliming. Det skal være like mange mellomrom foran denne som foran "pygeoapi" (som står nest øverst i filen) 

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


## 1.2 Kjør docker compose up -d på nytt

<details>
<summary>Finner du fylker og kommuner under [collections](http://localhost:5000/collections?f=html)?</summary>
I så fall er du flinkere enn oss. 🙂

Gå til [neste øving](oving3.md) så ser vi hvordan vi får pygeoapi til å faktisk bruke disse dataene.
</details>