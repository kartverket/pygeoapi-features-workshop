## Rydd opp igjen
I øvelsene vi har vært gjennom har vi opprettet objekter kjører eller som tar plass på pc'en. Vi tenkte det var hyggelig å la dere få mulighet til å rydde litt opp etter oss. Utover selve workshop-mappen gjelder det:

* containere
* images     - pygeoapi og postgis
* volume     - Dataene i databasen

Containere kan stoppes og images og volumes kan slettes ved bruk av applikasjonen docker desktop. 

Vi kan også kjøre en kommando for å stoppe alle containere, slette de nedlastede imagene og fjerne volumet som inneholder database-dataene.
Dette kan gjøres ved å kjøre kommando:


```
docker compose down --rmi all -v
```

Denne kommandoes vil stoppe og fjerne containere og slette images og volumer brukt i docker-compose filen vår.
Sletter alt inklusive image hentet fra docker.hub.


