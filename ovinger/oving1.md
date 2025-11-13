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
    image: geopython/pygeoapi:latest                # 'Sti' til image. Vi bruker 'latest' versjon her, men det er ofte lurt å spesifisere med versjonsnummer
    container_name: pygeoapi                        # valgfritt, men det er fint å sette eget container navn
    ports:
      - "5000:5000"                                 # Her 'mappes' port 5000 i containeren med port 5000 på pc'en din
    # volumes:
    #  - ./config.yml:/pygeoapi/local.config.yml    # Her spesifiserer vi at filen config.yml i denne mappen skal importeres inn i containeren
    restart: unless-stopped                         # Containeren restarter seg selv, med mindre den får en stopp-kommando. Eks. 'docker compose down'
```

## 2.2 På tide å kjøre API'et!
Containeren med pygeoapi kan nå enkelt kjøres opp ved å skrive følgende kommando inn i terminalen:

```docker compose up -d``` 

Kommandoen vil her hente ned image og bygge en container som kjører apiet. '-d' står for detatch. Uten denne vil prosessen leve i terminalen man kjørte kommandoen fra. 


## 2.3 Nå kan vi sjekke om det her funka! 
Om du vil ha en grafisk oversikt over containeren din hvor du kan se om den kjører, inspisere logger osv. kan du nå åpne programmet "docker desktop" dersom du har dette installert. Ta gjerne en titt!

Men det vi egentlig skal er å åpne en ny fane i favorittbrowseren din og skrive inn url'el:

```localhost:5000```

Du skal da få opp en side nettside som viser pygeoapi sin "html-visning". 🎉

Klikk deg gjerne litt rundt!

<details>
<summary>Ser det bra ut?</summary>
Eller har du kanskje spørsmål som
Hvem er Tom Kradis? Og hvorfor er det så mye rar informasjon og data her? 

Gå til [neste øving](oving2.md) så fikser vi det. 😃
</details>