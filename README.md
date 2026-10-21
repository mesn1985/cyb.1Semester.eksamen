
# 🐍 Eksamensprojekt – Programmering: Logdata fra B2B-integration

Dette repository indeholder eksempel-logfiler til programmeringsdelen af det tværfaglige eksamensprojekt på 1. semester af professionsbacheloruddannelsen i cybersikkerhed.

Logfilerne tager udgangspunkt i den virksomhed, der er beskrevet i semesterets fælles case.

Materialet skal anvendes som datagrundlag til udvikling og afprøvning af et Python-program, der kan indlæse, behandle og præsentere logdata fra virksomhedens IT-miljø.

---

## 📁 Repositoryets indhold

```text
exam-programming-data/
├── README.md
├── logs/
│   ├── linux_server.log
│   └── b2b_api.log
└── documentation/
    └── log_formats.md
```

Repositoryet indeholder to forskellige typer logfiler:

### 🖥️ `linux_server.log`

Logfilen indeholder registreringer fra virksomhedens lokale Linux-server, der anvendes til at drive B2B-integrationen.

Loggen indeholder blandt andet:

- Hændelser fra Linux-systemet og dets tjenester.
- Vellykkede og mislykkede SSH-login.
- Loginforsøg med ukendte brugernavne.
- Mislykkede autentificeringsforsøg ved brug af `sudo`.
- Hændelser relateret til driften af B2B-API'et.

Logfilen anvender et syslog-lignende format, hvor hver linje indeholder dato, tidspunkt, værtsnavn, proces og en hændelsesbesked.

### 🌐 `b2b_api.log`

Logfilen indeholder registreringer af forespørgsler til virksomhedens B2B-API.

API'et giver udvalgte erhvervskunder mulighed for at oprette ordrer, hente ordrestatus og anmode om annullering af ordrer.

Loggen indeholder blandt andet:

- Gennemførte API-forespørgsler.
- Mislykkede autentificeringsforsøg (`AUTHENTICATION_FAILED`).
- Afviste handlinger på grund af manglende rettigheder (`AUTHORIZATION_FAILED`).
- Ugyldige forespørgsler (`INVALID_REQUEST`).
- Forespørgsler afvist som følge af rate limiting (`RATE_LIMIT_EXCEEDED`).

Logfilen anvender et struktureret tekstformat med feltnavne og værdier adskilt af `=`. De enkelte felter er adskilt med `|`.

---

## 🎯 Formålet med logfilerne

De to logfiler skal anvendes til at udvikle og teste et Python-program, som kan behandle forskellige typer logdata.

Programmet skal kunne:

1. Indlæse logdata fra begge filer.
2. Fortolke loglinjerne og udtrække relevante oplysninger.
3. Organisere oplysningerne i hensigtsmæssige datastrukturer.
4. Identificere de sikkerhedsrelaterede hændelsestyper, der er beskrevet i eksamensopgaven.
5. Præsentere et struktureret overblik over logdata og sikkerhedsrelaterede hændelser for brugeren.
6. Håndtere fejl ved filindlæsning samt manglende eller ugyldigt formaterede oplysninger.

De konkrete krav til programmet fremgår af eksamensopgaven.

**Bemærk:** Repositoryet indeholder testdata og dokumentation, men ikke en færdig Python-løsning. I skal selv designe og implementere programmet.

---

## 📖 Dokumentation af logformater

I filen [`documentation/log_formats.md`](documentation/log_formats.md) findes en beskrivelse af de to logformater.

Dokumentationen beskriver blandt andet:

- Hvordan loglinjerne er opbygget.
- Hvilke oplysninger der kan udtrækkes.
- Betydningen af de enkelte felter.

Læs formatbeskrivelsen, inden I begynder at udvikle jeres program.

---

## 🧪 Anvendelse af testdata

Logfilerne er eksempler på data, som kan forekomme i virksomhedens IT-miljø.

I skal anvende filerne til at udvikle og afprøve jeres løsning.

Vær opmærksom på følgende:

- De to logfiler har forskellige formater.
- Linux-loggens hændelsesbeskeder varierer afhængigt af proces og hændelsestype.
- B2B-API-loggen indeholder både obligatoriske og valgfrie felter.
- Nogle API-forespørgsler er blevet afvist på grund af ugyldige oplysninger eller manglende rettigheder.
- Sikkerhedsrelaterede registreringer er ikke nødvendigvis dokumentation for et cyberangreb.

Programmet skal behandle logfilerne på baggrund af deres dokumenterede struktur og må ikke afhænge af bestemte værdier eller et fast antal loglinjer.

I må gerne supplere de udleverede filer med egne testdata for at undersøge, hvordan jeres program håndterer forskellige situationer.

---

## 🔐 Datasikkerhed og fiktive oplysninger

Alle logdata i repositoryet er fiktive og udarbejdet til undervisnings- og eksamensbrug.

Kunde-id'er, ordrenumre, produktnumre, brugernavne og hændelser er syntetiske.

De eksterne IP-adresser, der anvendes i eksemplerne, tilhører dokumentationsområder. Interne adresser bruges til at illustrere virksomhedens lokale netværk.

Logfilerne indeholder ikke virkelige adgangskoder eller API-nøgler.

---

## ✅ Afgrænsning

Formålet med programmeringsopgaven er at udvikle et værktøj til indlæsning, parsing, strukturering og præsentation af logdata.

Det er ikke et krav at udvikle et avanceret SIEM-system, implementere automatisk angrebsdetektion eller afgøre, om de registrerede hændelser udgør faktiske cyberangreb.

Programmet skal kunne identificere de dokumenterede hændelsestyper og præsentere relevante oplysninger, så en bruger kan undersøge dem nærmere.
