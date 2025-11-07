# Øving 2
I denne øvingen skal vi faktisk legge til data våre egne data og sette opp konfigurasjonsfilen for pygeoapi. 
Vi har valgt å bruke datasettet 'Administrative enheter" som kan lastes ned på geonorge. Pygeoapi kan bruke diverse filer som kildedata, men som oftest benytter man en databaseserver. 


Her hjelper vi dere litt.. 
Vi har lastet ned datasettet som en "postgis dump" fra geonorge.no lagt til oppsett for å flytte dataene inn i en postgis database. 

Dette opsettet ligger under mappen 'postgis'. Gå videre til neste steg for å ta det i bruk. 


## 1.1 Lim dette inn nederst i docker-compose.yaml filen

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
## 1.2 Kjør docker compose up -d på nytt
Det vil da kjøres opp en container til som inneholder en postgis database med data for administrative enheter. 
Det vil fortsatt ikke være noen kobling mellom pygeoapi og datakilden

## 