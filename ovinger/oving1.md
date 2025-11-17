# Øving 1
En enkel måte å kjøre pygeoapi på er ved å bruke pygeoapi sitt docker image. Et image kan bygges til en container som kjører lokalt på pc'en din.
I denne øvingen skal vi bruke docker compose for å kjøre pygeoapi sitt image som en container på din pc.

## 1.1 Lag en en fil som heter docker-compose.yaml
1. Lag en fil som heter "docker-compose.yaml"
Denne filen skal spesifisere hvordan pygeoapi skal kjøres. 
Kopier dette inn i filen:

```yml
services:
  pygeoapi:
    image: geopython/pygeoapi:latest
    container_name: pygeoapi
    ports:
      - "5000:80"
    restart: unless-stopped
```

## 1.2 På tide å kjøre API'et!
Dette er faktisk alt vi trenger for å få opp _noe_. 

Containeren med pygeoapi kan nå enkelt kjøres opp ved å skrive følgende kommando inn i terminalen:

```
docker compose up -d
```

Kommandoen vil her hente ned image og bygge en container som kjører apiet. '-d' står for detatch. Uten denne vil prosessen leve i terminalen man kjørte kommandoen fra. 

For å se at du har en container kjørende, skriv gjerne inn:
```docker ps``` i terminalen og se hva det står under status. ("Up" er bra)


Du kan også teste ut 'docker-compose up" uten å angi '-d'. Bruk ctrl-c for å avslutte.

## 1.3 Nå kan vi sjekke om det her funka! 
Om du vil ha en grafisk oversikt over containeren din hvor du kan se om den kjører, inspisere logger osv. kan du nå åpne programmet "docker desktop" dersom du har dette installert. Ta gjerne en titt!

Men det vi egentlig skal er å åpne en ny fane i favorittbrowseren din og skrive inn url'el:

```localhost:5000```

Du skal da få opp en side nettside som viser pygeoapi sin "html-visning". 🎉

Klikk deg gjerne litt rundt! 

## 1.4 Hva nå?
Ser det bra ut?  
Eller har du kanskje spørsmål som "Hvem er Tom Kralidis?" og "Hvorfor er det så mye rar informasjon og data her?" 

Gå til [neste øving](oving2.md) så fikser vi det. 😃

## 1.5 Andre nyttige docker kommandoer

* `docker compose up -d` starter containerne i docker-compose fila og bygger den dersom den ikke er bygget før
* `docker compose up -d --build` starter containerne i docker-compose fila og bygger ny container. (Man trenger --build dersom man har gjort endringer på sevlve imaget, eks. endringer i en Dockerfile)
* `docker compose start` starter containerne i docker-compose fila
* `docker compose stop`   stopper containerne i docker-compose fila
* `docker compose restart` restarter containerne i docker-compose fila
* `docker compose down` fjerner containerne i docker-compose fila


<details>
<summary>Fasit</summary>
Du skal nå ha en fil "docker-compose.yml" i hovedmappen in. (Mappen ett hakk over ovinger-mappen).
Innholdet i filen skal være:

```yml
services:
  pygeoapi:
    image: geopython/pygeoapi:latest                # 'Sti' til image. Vi bruker 'latest' versjon her, men det er ofte lurt å spesifisere med versjonsnummer
    container_name: pygeoapi                        # valgfritt, men det er fint å sette eget container navn
    ports:
      - "5000:80"                                   # Her 'mappes' port 80 i containeren med port 5000 på pc'en din
    restart: unless-stopped                         # Containeren restarter seg selv, med mindre den får en stopp-kommando. Eks. 'docker compose down'

```
</details>