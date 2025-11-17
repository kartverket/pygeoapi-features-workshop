# Øving 5 – Bonus: Eksperimenter med Templating i pygeoapi

I denne øvingen skal du utforske hvordan pygeoapi kan tilpasses med egne maler (templates) og statiske filer. Dette gir deg mulighet til å endre utseende og funksjonalitet på API-et, for eksempel for å tilpasse HTML-visningen eller legge til egne bilder og CSS.


## 5.1 Rediger docker-compose.yml og config.yml

For å legge til templates som du selv kan redigere, mounter vi config-filen inn som tidligere, men i tillegg har vi `static/` og `templates/` mappene som skal mountes:

```
  volumes:
    - ./config.yml:/pygeoapi/local.config.yml
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
- Gå inn i `templates/`-mappen i løsningen. Finn filen `landing_page.html`.
- Gjør endringer i `landing_page.html` (f.eks. legg til en tekst, endre overskrift, eller sett inn et bilde/logo fra `static/img/`).
- Endre på CSS i `static/css/default.css` for å tilpasse utseendet.
- Bytt ut `organization_logo.png` med din egen organisasjonslogo (men behold filnavnet).
- Lagre filene og last inn siden på nytt i nettleseren. (Du må kanskje restarte pygeoapi-containeren: `docker compose restart pygeoapi`)


## 5.3 Oppsummering

Med templating og statiske filer kan du tilpasse pygeoapi til å passe din organisasjon eller ditt prosjekt. Prøv deg frem og se hvor mye du kan endre på utseendet!

> 💡 Husk: Endringer i maler og statiske filer krever ofte restart av pygeoapi-containeren for å tre i kraft.

---

**Tips:**
- [pygeoapi docs: Customizing templates](https://docs.pygeoapi.io/en/latest/configuration.html#templates)
- [Jinja2 template syntax](https://jinja.palletsprojects.com/en/3.1.x/templates/)
