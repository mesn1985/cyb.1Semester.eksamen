# Dokumentation af logformater

Dette dokument beskriver format, felter og hændelsestyper i følgende logfiler:

- `linux_server.log`
- `b2b_api.log`

Logfilerne indeholder syntetiske registreringer fra virksomhedens lokale Linux-server og B2B-integration.

De to logfiler har forskellig opbygning og indeholder både almindelige driftshændelser og sikkerhedsrelaterede registreringer.

---

## 1. Linux-serverlog – `linux_server.log`

### 1.1 Beskrivelse

Logfilen indeholder hændelser fra virksomhedens lokale Linux-server, som anvendes til at drive B2B-API'et.

Registreringerne stammer blandt andet fra følgende processer og tjenester:

| Proces eller tjeneste | Beskrivelse |
|---|---|
| `sshd` | SSH-forbindelser, loginforsøg og sessioner |
| `sudo` | Forsøg på at udføre handlinger med forhøjede rettigheder |
| `systemd` | Start, stop og overvågning af systemtjenester |
| `kernel` | Hændelser fra Linux-kernen |
| `CRON` | Planlagte opgaver |
| `b2b-api` | Registreringer fra B2B-applikationen |

### 1.2 Logformat

Logfilen anvender et syslog-lignende tekstformat.

**Eksempel:**

```text
Oct 21 08:15:11 b2b-server01 sshd[1902]: Failed password for root from 203.0.113.45 port 48920 ssh2
```

Den overordnede struktur er:

```text
MONTH DAY TIME HOSTNAME PROCESS[PID]: MESSAGE
```

Proces-ID (`PID`) er valgfrit.

| Felt | Beskrivelse | Eksempel |
|---|---|---|
| Måned | Forkortet engelsk månedsnavn | `Oct` |
| Dag | Dag i måneden | `21` |
| Tidspunkt | Tid i formatet `HH:MM:SS` | `08:15:11` |
| Værtsnavn | Serverens navn | `b2b-server01` |
| Proces | Proces eller tjeneste, der registrerer hændelsen | `sshd` |
| Proces-ID | Processens numeriske ID, hvis det er angivet | `1902` |
| Besked | Beskrivelse af den registrerede hændelse | `Failed password for root ...` |

Tidsstemplet indeholder ikke årstal eller tidszone.

### 1.3 Variation i logformatet

Nogle registreringer har et proces-ID:

```text
Oct 21 08:02:14 b2b-server01 sshd[1842]: Accepted publickey for itadmin from 192.168.10.25 port 53210 ssh2
```

Andre registreringer har ikke et proces-ID:

```text
Oct 21 08:50:12 b2b-server01 kernel: eth0: Link is Down
```

Beskedfeltets indhold varierer afhængigt af processen og hændelsen.

Eksempelvis har SSH-beskeder en anden opbygning end registreringer fra `systemd` eller `kernel`.

### 1.4 SSH-hændelser

Logfilen indeholder forskellige hændelser relateret til SSH-adgang.

**Mislykket login med adgangskode**

```text
Failed password for root from 203.0.113.45 port 48920 ssh2
```

Beskeden angiver et mislykket loginforsøg med adgangskode.

Oplysningerne omfatter blandt andet brugernavn, kilde-IP-adresse og portnummer.

**Vellykket login med offentlig nøgle**

```text
Accepted publickey for itadmin from 192.168.10.25 port 53210 ssh2
```

Beskeden angiver, at et SSH-login med offentlig nøgle blev accepteret.

**Loginforsøg med ukendt bruger**

```text
Invalid user administrator from 198.51.100.18 port 51100
```

Beskeden angiver et loginforsøg med et brugernavn, som systemet ikke genkender.

En beslægtet registrering kan eksempelvis se sådan ud:

```text
Failed password for invalid user administrator from 198.51.100.18 port 51102 ssh2
```

Denne besked angiver et mislykket adgangskodeforsøg for et ugyldigt brugernavn.

**SSH-sessioner**

Logfilen indeholder også registreringer om åbning og lukning af SSH-sessioner:

```text
pam_unix(sshd:session): session opened for user itadmin
```

```text
pam_unix(sshd:session): session closed for user itadmin
```

### 1.5 Sudo-hændelser

Logfilen indeholder registreringer om brug af `sudo`.

Eksempel på mislykket autentificering:

```text
pam_unix(sudo:auth): authentication failure; user=operator
```

Eksempel på en opsummerende registrering af forkerte adgangskodeforsøg:

```text
operator : 3 incorrect password attempts ; TTY=pts/1 ; USER=root ; COMMAND=/usr/bin/systemctl restart b2b-api
```

Den sidste besked indeholder blandt andet oplysninger om brugeren, terminalen, den ønskede brugeridentitet og kommandoen.

### 1.6 System- og driftshændelser

Logfilen indeholder også registreringer, der vedrører den almindelige drift.

**Systemtjenester**

```text
Started B2B API Service.
```

```text
b2b-api.service: Failed with result 'exit-code'.
```

**Netværksforbindelse**

```text
eth0: Link is Down
```

```text
eth0: Link is Up
```

**Applikationshændelser**

```text
Warning: slow response from order backend
```

Disse registreringer beskriver tilstande eller hændelser i serverens drift.

---

## 2. B2B-API-log – `b2b_api.log`

### 2.1 Beskrivelse

Logfilen indeholder registreringer af forespørgsler til virksomhedens B2B-API.

API'et understøtter følgende handlinger:

| Handling | Beskrivelse |
|---|---|
| `CREATE_ORDER` | Oprettelse af en ordre |
| `GET_ORDER_STATUS` | Forespørgsel på en ordres status |
| `CANCEL_ORDER` | Anmodning om annullering af en ordre |

En loglinje repræsenterer én registreret API-forespørgsel.

### 2.2 Logformat

API-loggen anvender et tekstformat med navngivne felter.

**Eksempel:**

```text
timestamp=2026-10-21T08:05:40|client_id=C1001|source_ip=198.51.100.21|action=CREATE_ORDER|request_id=REQ-0001|order_id=ORD-5001|product_id=PRD-101|quantity=5|auth_status=AUTHENTICATED|result=SUCCESS
```

Felterne er adskilt af tegnet `|`.

Hvert felt består af et feltnavn og en værdi adskilt af `=`.

Eksempel:

```text
client_id=C1001
```

Her er `client_id` feltnavnet, og `C1001` er værdien.

Felterne identificeres ved deres navne. Deres rækkefølge er ikke en del af formatets betydning.

### 2.3 Felter

| Felt | Beskrivelse | Eksempel |
|---|---|---|
| `timestamp` | Tidspunktet for registreringen | `2026-10-21T08:05:40` |
| `client_id` | Oplyst eller verificeret klient-ID | `C1001` |
| `source_ip` | Kilde-IP-adresse | `198.51.100.21` |
| `action` | Den ønskede API-handling | `CREATE_ORDER` |
| `request_id` | Identifikation af API-forespørgslen | `REQ-0001` |
| `order_id` | Ordrenummer | `ORD-5001` |
| `product_id` | Produktnummer | `PRD-101` |
| `quantity` | Angivet antal produkter | `5` |
| `auth_status` | Autentificeringsstatus | `AUTHENTICATED` |
| `result` | Resultatet af behandlingen | `SUCCESS` |
| `reason` | Supplerende årsag til fejl eller afvisning | `INVALID_API_KEY` |

### 2.4 Obligatoriske og valgfrie felter

Følgende felter indgår i alle korrekt formaterede registreringer:

- `timestamp`
- `client_id`
- `source_ip`
- `action`
- `request_id`
- `auth_status`
- `result`

Følgende felter kan forekomme afhængigt af forespørgslen og dens resultat:

- `order_id`
- `product_id`
- `quantity`
- `reason`

Eksempelvis indeholder en gennemført ordreoprettelse normalt oplysninger om produktnummer og antal.

En forespørgsel, der afvises under autentificeringen, kan mangle ordre- og produktoplysninger.

### 2.5 Værdier og formater

| Felt | Format eller værditype |
|---|---|
| `timestamp` | Dato og tidspunkt på formen `YYYY-MM-DDTHH:MM:SS` |
| `client_id` | Klientidentifikation, eksempelvis `C1001`, eller `UNKNOWN` |
| `source_ip` | IPv4-adresse i de udleverede eksempler |
| `action` | En af de tre dokumenterede API-handlinger |
| `request_id` | Tekstidentifikation, eksempelvis `REQ-0001` |
| `order_id` | Tekstidentifikation, eksempelvis `ORD-5001` |
| `product_id` | Tekstidentifikation, eksempelvis `PRD-101` |
| `quantity` | Den modtagne værdi for antal produkter |
| `auth_status` | `AUTHENTICATED` eller `FAILED` |
| `result` | En af de dokumenterede resultatkoder |
| `reason` | Supplerende årsagskode |

Alle værdier er repræsenteret som tekst i logfilen.

Feltet `quantity` kan eksempelvis indeholde `5`, `0`, `-2` eller `abc`. Disse værdier afspejler, hvad der blev registreret i forbindelse med forespørgslen, herunder ugyldigt input.

Tidsstemplet indeholder ikke en eksplicit tidszone.

### 2.6 Autentificering og autorisation

API'et benytter API-nøgler til autentificering af klienter.

**Autentificering** handler om at bekræfte klientens identitet.

**Autorisation** handler om, hvilke handlinger eller ressourcer den autentificerede klient har adgang til.

Feltet `auth_status` kan have følgende værdier:

| Værdi | Betydning |
|---|---|
| `AUTHENTICATED` | Klientens identitet er blevet bekræftet |
| `FAILED` | Klienten blev ikke autentificeret, eller forespørgslen blev afvist før en bekræftet autentificering |

Ved mislykket autentificering kan `client_id` være et klient-ID, som forespørgslen selv har oplyst. Det er i så fald ikke en verificeret identitet.

Værdien `UNKNOWN` betyder, at der ikke er registreret et identificerbart klient-ID.

### 2.7 Resultatkoder

| Resultat | Betydning |
|---|---|
| `SUCCESS` | Forespørgslen blev gennemført |
| `NOT_FOUND` | Den ønskede ressource blev ikke fundet |
| `SERVER_ERROR` | API'et registrerede en intern fejl |
| `AUTHENTICATION_FAILED` | Klientens identitet kunne ikke bekræftes |
| `AUTHORIZATION_FAILED` | Klienten havde ikke tilladelse til den ønskede handling |
| `INVALID_REQUEST` | Forespørgslen indeholdt ugyldige oplysninger |
| `RATE_LIMIT_EXCEEDED` | Forespørgslen blev afvist på grund af en begrænsning i antallet af forespørgsler |

### 2.8 Årsagskoder

Feltet `reason` giver yderligere oplysninger om bestemte fejl og afvisninger.

| Årsagskode | Betydning |
|---|---|
| `INVALID_API_KEY` | Den anvendte API-nøgle blev ikke godkendt |
| `MISSING_API_KEY` | Forespørgslen manglede en API-nøgle |
| `ORDER_NOT_OWNED` | Klienten har ikke adgang til den angivne ordre |
| `INVALID_QUANTITY` | Det angivne antal produkter er ugyldigt |
| `TOO_MANY_REQUESTS` | Grænsen for antallet af forespørgsler er overskredet |

### 2.9 Eksempler på registreringer

**Vellykket ordreoprettelse**

```text
timestamp=2026-10-21T08:05:40|client_id=C1001|source_ip=198.51.100.21|action=CREATE_ORDER|request_id=REQ-0001|order_id=ORD-5001|product_id=PRD-101|quantity=5|auth_status=AUTHENTICATED|result=SUCCESS
```

**Mislykket autentificering med ugyldig API-nøgle**

```text
timestamp=2026-10-21T11:15:02|client_id=C1002|source_ip=203.0.113.89|action=GET_ORDER_STATUS|request_id=REQ-0042|order_id=ORD-5002|auth_status=FAILED|result=AUTHENTICATION_FAILED|reason=INVALID_API_KEY
```

**Manglende autorisation**

```text
timestamp=2026-10-21T08:21:30|client_id=C1005|source_ip=198.51.100.25|action=CANCEL_ORDER|request_id=REQ-0010|order_id=ORD-5001|auth_status=AUTHENTICATED|result=AUTHORIZATION_FAILED|reason=ORDER_NOT_OWNED
```

**Ugyldigt antal produkter**

```text
timestamp=2026-10-21T10:36:10|client_id=C1004|source_ip=198.51.100.24|action=CREATE_ORDER|request_id=REQ-0033|order_id=ORD-5014|product_id=PRD-101|quantity=abc|auth_status=AUTHENTICATED|result=INVALID_REQUEST|reason=INVALID_QUANTITY
```

**Rate limiting**

```text
timestamp=2026-10-21T10:25:22|client_id=UNKNOWN|source_ip=203.0.113.45|action=GET_ORDER_STATUS|request_id=REQ-0029|auth_status=FAILED|result=RATE_LIMIT_EXCEEDED|reason=TOO_MANY_REQUESTS
```

---

## 3. Om logdataene

### 3.1 Registrerede fejl og ugyldige forespørgsler

Logfilerne indeholder hændelser, hvor en handling eller forespørgsel er mislykkedes.

Eksempelvis angiver:

```text
result=AUTHENTICATION_FAILED
```

at API'et registrerede en mislykket autentificering.

Tilsvarende angiver:

```text
result=INVALID_REQUEST
```

at API'et registrerede en forespørgsel med ugyldige oplysninger.

Sådanne registreringer kan være korrekt formaterede loglinjer, selvom de beskriver fejl i den oprindelige forespørgsel.

### 3.2 Sikkerhedsrelaterede registreringer

Begge logfiler indeholder registreringer, som kan være relevante i en sikkerhedsmæssig sammenhæng.

Det omfatter blandt andet adgangsforsøg, afviste API-nøgler, manglende rettigheder og rate limiting.

En registreret fejl eller afvisning er dog ikke i sig selv dokumentation for et cyberangreb.

### 3.3 Fiktive data

Alle registreringer er syntetiske og udarbejdet til undervisnings- og eksamensbrug.

Kunde-ID'er, ordrenumre, produktnumre, brugernavne og hændelsesforløb er fiktive.

De eksterne IP-adresser anvender reserverede dokumentationsområder, og de interne adresser illustrerer lokale netværksforbindelser.

Logfilerne indeholder ingen faktiske adgangskoder eller API-nøgler.