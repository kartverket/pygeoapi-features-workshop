# Bonusøving: laste ned data ved bruk av gdal
Ikke alle vil ha gdal installert, men om kommandoen:

```gdalinfo --version```

Gir en versjon og ikke en feilmelding, så skal du være good to go. Ev. må gdal lastes ned og/eller legges til i path før man kan gjøre denne øvelsen.å+

Dataene fra et ogc-api vises på geojsonformat og kan relativt enkelt lastes ned som en geojson. Dersom man derimot ønsker et annet filformat ville man måtte konvertere nedlastet json-fil etter nedlastning. Et alternativ for en "one-stop-solution" kan være å benytte GDAL verktøyet "ogr2ogr" som har mulighet til å benytte ogc api features som input og konvertere til ønsket filformat. Under vil vi; 
1. Benytte ogrinfo til å sjekke tilgjendelige lag i features tjenesten vår
2. Benytte ogr2ogr til å laste ned data til en geopackage-fil


## 6.1 Last ned dataene fra ogc api'et til en geopackage-fil
### 6.1.1 Sjekk hvilke lag som finnes i tjenesten
Dersom pygeoapi kjører på localhost:5000, så vil følgende kommando liste opp tilgjengelige lag i tjenesten:

```ogrinfo OAPIF:http://localhost:5000```

Output fra kommandoen skal se omtrent slik ut: 
``` 
1: fylker (title: Fylker) (Multi Polygon)
2: kommuner (title: Kommuner) (Multi Polygon)
```

Hvis vi nå ønsker å benytte gdal til å laste ned dette til ønsket format kan vi bruke ogr2ogr.

### 6.1.2 Last ned kommuner og fylker som en geopackage-fil
ogr2ogr er en kommando som kan konvertere mellom en rekke ulike geografiske filformater. (Kommandoen ```ogr2ogr --formats``` vil liste ut støttede formater)
Vi velger å bruke den til å laste ned kommuner fra api'et vårt til en geopackage fil:


```
 ogr2ogr -f GPKG mine_kommuner.gpkg \
  "OAPIF:http://localhost:5000" \
  kommuner   
```
Der:
* -f GPKG - angir filformat vi vil ha ut
* mine_kommuner.gpkg - er filnavnet på filen
* "OAPIF:http://localhost:5000" - spesifiserer at vi skal lese fra et OGC API på localhost
* kommuner - er laget vi ønsker å laste ned 

Man kan spesifisere flere lag og man vil (litt avhengig av filformat ut). Så dersom vi vil ha både kommuner og fylker ut kan man bruke kommandoen:

```
 ogr2ogr -f GPKG mine_kommuner_og_fylker.gpkg \
  "OAPIF:http://localhost:5000" \
  kommuner \
  fylker
```

> Tips! 💡
> Geopackage er bygget på sqllite. Så om du eks. har en sqliteviewer i VSCode så kan du lett inspisere den nedlastede filen. 
> 