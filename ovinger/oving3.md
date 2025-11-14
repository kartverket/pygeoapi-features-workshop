# Øving 3
Pygeoapi trenger å vite hvor den skal lete etter data. I forrige øving kjørte vi opp en database, men vi "glemte" å konfiguere pygeoapi til å bruke disse dataene. Det skal vi fikse nå.


## 3.1 Benytt egen konfigurasjonsfil for pygeoapi
Konfigurasjonsfilen styrer en del ting som metadata og koblig til datakilde.
Det blir fort litt mye å fylle ut og vi har derfor laget en ferdig utfyllt config-fil i mappen "config".
Ta gjerne en titt i filen og spesielt det som er under "resources". Her spesifiserer vi datasettene våre, som i dette tilfellet er kommuner og fylker.

For å ta denne configfilen i bruk, så mounter vi den inn i pygeoapi-containeren på container-stien: /pygeoapi/local.config.yml

Gå til docker-compose filen og lim inn følgende for å spesifisere at configfil skal mountes inn som et volum:
```yml
    volumes:
     - ./config.yml:/pygeoapi/local.config.yml      # Her spesifiserer vi at filen config.yml i denne mappen skal importeres inn i containeren
```
Det skal limes inn under pygeoapi tjenesten. Eks. under "ports" seksjonen.

<details>
<summary>Eksempel</summary>

```yml
...
        pygeoapi:
          image: geopython/pygeoapi:latest                # 'Sti' til image. Vi bruker 'latest' versjon her, men det er ofte lurt å spesifisere med versjonsnummer
          container_name: pygeoapi                        # valgfritt, men det er fint å sette eget container navn
          ports:
            - "5000:80"                                   # Her 'mappes' port 80 i containeren med port 5000 på pc'en din
Her  --> volumes:
Her  -->  - ./config.yml:/pygeoapi/local.config.yml      # Her spesifiserer vi at filen config.yml i denne mappen skal brukes inn i containeren
          restart: unless-stopped                         # Containeren restarter seg selv, med mindre den får en stopp-kommando. Eks. 'docker compose down'
          environment:

...
```
</details>

Når disse to linjene er lagt inn vil det være en koblig mellom filen config.yml i dette workspacet og filen local.config.yml som lever inne i containeren.
Du kan derfor redigere filen config.yml som du vil og pygeoapi får meg seg endringene. Du er imidlertidig nødt til å restarte pygeoapi hver gang du gjør endringer i filen, da pygeoapi leser denne filen inn ved oppstart. (Dersom den hadde lest filen 'dynamisk' hadde du ikke trengt å restarte pygeoapi ved endring av filen)

Kjør kommandoen ```docker compose up -d``` for å starte på nytt med endringene vi har gjort. 
For videre endringer i filen _config.yml_ så holder det å skrive ```docker compose restart pygeoapi``` for å restarte pygeoapi med oppdatert config. Men om det gjøres endringer i filen docker-compose.yaml, så må "docker compose up -d" kjøres. 

Du kan nå åpne åpne ```localhost:5000``` i nettleseren igjen og se om det har skjedd noe.

> 💡 **Tips:** Inspiser docker desktop eller skriv kommandoen ```docker ps``` for oversikt over kjørende containere. 
> Vi skal nå ha 2 kjørende containere, 1 for pygeoapi og 1 for databaen vår.


