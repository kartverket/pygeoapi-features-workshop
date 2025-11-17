# Øving 7 – Bonus: Test ulike OGC API Features-endepunkter med testside.html

Vi har lagd en liten testside der du kan eksperimentere med ulike OGC API Features-tjenester. Du kan enkelt bytte base URL og teste filtermuligheter mot forskjellige endepunkter.

## 7.1 Start testside.html

Gå til mappen `assets/oving7 - testside/` og åpne `testside.html` i nettleseren din (for eksempel ved å høyreklikke og velge "Åpne i nettleser").

> 💡 Du trenger ikke å kjøre noen server for å bruke denne siden, så lenge OGC API Features-endepunktet du peker mot er tilgjengelig fra din maskin.

## 7.2 Bytt base URL

Øverst på siden finner du et felt for "OGC API Base URL". Her kan du lime inn adressen til ulike OGC API Features-tjenester du vil teste mot (default er din lokale pygeoapi som du nettopp har satt opp).

- Skriv inn ønsket base URL og trykk "Apply".
- Siden vil automatisk hente collections og tilgjengelige filtermuligheter fra valgt endepunkt.

URLer til forskjellige OGC-apier som kan testes - __NB: Spørringer mot features-tjenester kan være tunge - bruk gjerne filter-funksjoner og bounding box (zoom inn på kartet) for å spørre om begrenset mengde data__: 
 - OpenStreetMap - Daraa - https://ri.ldproxy.net/daraa
 - Vingårder i Tyskland - https://ri.ldproxy.net/vineyards
 - Pdok - kadaster - https://api.pdok.nl/kadaster/3d-basisvoorziening/ogc/v1
 - Riksantikvaren - kulturmiljøer - https://api.ra.no/kulturmiljoer

## 7.3 Prøv filter

- For hver collection vises dynamiske filterfelt basert på hvilke attributter (queryables) som er tilgjengelige.
- Skriv inn filterverdier (eller huk av for boolean-felt) og trykk "Load Data" for å hente data med filteret aktivt.

## 7.4 Utforsk kart og data

- Pan og zoom i kartet for å se hvordan bbox påvirker hvilke data som hentes.
- Klikk på objekter i kartet for å se detaljer i sidepanelet.
- Prøv ulike OGC API Features-endepunkter og sammenlign hvordan de støtter filter og data.

## 7.5 Ekstra: Feilsøking og tips

- Hvis du ikke får opp data, sjekk at base URL er korrekt og at tjenesten er tilgjengelig.
- Noen endepunkter kan ha begrenset støtte for filter eller bbox – prøv ulike kombinasjoner.
- Se i nettleserens konsoll (F12) for å se hvilke spørringer som sendes og eventuelle feilmeldinger.