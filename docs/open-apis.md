# Open APIs — Curated List and Lab Integration

> Real data makes labs stickier. When your PostgreSQL index optimization runs
> against actual IBGE census data instead of `generate_series`, the result
> means something. This document lists the best open APIs and shows exactly
> which lab each one enhances.

---

## Brazilian Government APIs

These are particularly good for your portfolio — using real Brazilian data
signals local context to local clients while demonstrating technical depth
internationally.

---

### BrasilAPI
**URL**: https://brasilapi.com.br  
**Auth**: None  
**Rate limit**: Generous, no key needed  
**What it aggregates**:

```
GET /api/cep/v2/{cep}               → Address lookup by postal code
GET /api/cnpj/v1/{cnpj}             → Company data by CNPJ (name, address, status)
GET /api/banks/v1                   → All Brazilian banks (300+ entries)
GET /api/banks/v1/{code}            → Specific bank details
GET /api/holiday/v1/{year}          → Brazilian national holidays by year
GET /api/ibge/municipios/v1/{uf}    → All cities in a state
GET /api/ibge/uf/v1                 → All states with codes
GET /api/fipe/tabela/v1             → FIPE vehicle price tables
GET /api/fipe/preco/v1?...          → Vehicle prices by make/model/year
GET /api/pix/v1/participants        → All PIX-enabled banks
GET /api/taxas/v1                   → Current Selic, CDI, IPCA rates
```

**Best for**: Lab 00-A (CPF/CNPJ validation), Lab 03 (REST API design), Lab 08 (PostgreSQL with real data)

---

### BACEN (Brazilian Central Bank) Open Data
**URL**: https://dadosabertos.bcb.gov.br  
**Auth**: None  
**What it has**:

```
GET /api/dados/serie/bcdata.sgs.{code}/dados
  → Time series data for any economic indicator

Common series codes:
  432  → Selic rate (daily)
  433  → IPCA accumulated (monthly inflation)
  1  → USD/BRL exchange rate (daily)
  7478 → INPC (inflation for low-income population)
  11  → CDI rate (daily)
  7454 → GDP growth quarterly
```

**Example**:
```bash
# Get last 30 days of USD/BRL exchange rate
curl "https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarPeriodo(dataInicial=@dataInicial,dataFinalCotacao=@dataFinalCotacao)?@dataInicial='01-01-2025'&@dataFinalCotacao='01-31-2025'&\$format=json"
```

**Best for**: Lab 12 (Kafka — stream exchange rate events), Lab 08 (PostgreSQL time-series queries), Lab 06 (Grafana — visualize economic data)

---

### IBGE (Brazilian Institute of Geography and Statistics)
**URL**: https://servicodados.ibge.gov.br/api/docs  
**Auth**: None  

```
GET /api/v1/localidades/estados           → All 27 states
GET /api/v1/localidades/municipios        → All ~5570 municipalities
GET /api/v3/agregados/{id}/periodos/{p}/variaveis/{v}
  → Census aggregates (population, income, education)

Useful aggregate IDs:
  1301 → Population by city (2022 census)
  5938 → GDP per capita by city
```

**Best for**: Lab 08 (PostgreSQL with real 5570-row city dataset + census data)

---

### Dados Abertos (Federal Government Portal)
**URL**: https://dados.gov.br  
**What it has**: Datasets from hundreds of federal agencies.  

Notable datasets for labs:
- **Medicamentos ANVISA** — drug registry (~80k records, great for PostgreSQL full-text search lab)
- **Contratos Federais** — government contracts (financial data, good for aggregation)
- **CEIS** — companies barred from government contracts (sanction list)

**Best for**: Lab 08 (realistic dataset with >100k rows)

---

## Finance & Economics

### Open Exchange Rates
**URL**: https://openexchangerates.org  
**Auth**: Free tier (1000 req/month, hourly rates)  

```
GET /api/latest.json?app_id=YOUR_KEY  → Latest rates for 200 currencies
GET /api/historical/{date}.json       → Historical rates
GET /api/currencies.json              → Currency names
```

**Best for**: Lab 12 (Kafka producer — stream currency events), Lab 09 (Redis cache — TTL = 1 hour matches API update frequency)

### Awesome Hledger / Frankfurter
**URL**: https://www.frankfurter.app  
**Auth**: None (free, no key)  

```
GET /latest?from=BRL&to=USD,EUR    → Current exchange rates
GET /2024-01-01?from=BRL           → Historical rate
GET /2024-01-01..2024-12-31        → Date range
```

**Best for**: Lab 03 (REST API wrapper), Lab 20 (AI API — "convert my receipt in BRL to USD")

---

## Geography & Location

### ViaCEP
**URL**: https://viacep.com.br  
**Auth**: None  

```
GET /ws/{cep}/json    → Address for a Brazilian postal code
GET /ws/{uf}/{city}/{street}/json   → CEP lookup by address
```

**Best for**: Lab 00-A (real-world validation — CEP format check + API verification), Lab 03

### Nominatim (OpenStreetMap)
**URL**: https://nominatim.openstreetmap.org  
**Auth**: None (but add User-Agent header and respect 1 req/s)  

```
GET /search?q=Brasilia+DF&format=json    → Geocode
GET /reverse?lat=-15.77&lon=-47.92&format=json  → Reverse geocode
```

**Best for**: Lab 03 (REST API design — add geolocation to a resource), Lab 22 (HomeHub — map sensor locations)

---

## Public Data & APIs

### NASA APIs
**URL**: https://api.nasa.gov  
**Auth**: Free API key (no credit card)  

```
GET /planetary/apod?api_key=KEY        → Astronomy Picture of the Day
GET /neo/rest/v1/feed?api_key=KEY      → Near-Earth Objects (asteroids)
GET /DONKI/notifications?api_key=KEY   → Space weather notifications
```

**Best for**: Lab 12 (Kafka — stream daily APOD events), Lab 06 (Grafana — asteroid count over time as practice data)

### REST Countries
**URL**: https://restcountries.com  
**Auth**: None  

```
GET /v3.1/all                      → All 250 countries
GET /v3.1/name/{name}              → Search by name
GET /v3.1/region/{region}          → By region
```

**Best for**: Lab 01 (TypeScript types — model the complex Country object), Lab 03 (demonstrate filtering/pagination design)

### Open Library
**URL**: https://openlibrary.org/developers/api  
**Auth**: None  

```
GET /api/books?bibkeys=ISBN:{isbn}&format=json  → Book details
GET /search.json?q={query}&limit=10             → Search books
GET /works/{id}.json                             → Full work record
```

**Best for**: Lab 03 (build a book collection API that enriches with Open Library data), Lab 10 (MongoDB document model for books)

---

## Weather & Environment

### Open-Meteo
**URL**: https://open-meteo.com  
**Auth**: None (free, no key for non-commercial)  

```
GET /v1/forecast?latitude=-15.77&longitude=-47.92&hourly=temperature_2m,humidity
  → 7-day forecast for Brasília (or any coordinate)
GET /v1/archive?latitude=-15.77&longitude=-47.92&start_date=2024-01-01&end_date=2024-12-31&daily=temperature_2m_max
  → Historical weather
```

**Best for**: Lab 24 (Arduino sensors — compare your DHT22 readings with Open-Meteo for Brasília as a sanity check), Lab 06 (Grafana — overlay outdoor temp with indoor sensor data)

---

## Payments

### Stripe (Test Mode)
**URL**: https://stripe.com/docs/api
**Auth**: Test API keys (no real money, no credit card needed to get keys)

```
POST /v1/checkout/sessions          → Create a payment page (Checkout)
POST /v1/payment_intents            → Create a payment intent (custom UI)
GET  /v1/payment_intents/{id}       → Check payment status
POST /v1/refunds                    → Refund a payment
Webhooks: payment_intent.succeeded, checkout.session.completed
```

**Why Stripe specifically**: It's the international standard. Every company
you'd work for abroad either uses Stripe or something that works like it.
The test mode is fully functional — you can create payments, trigger webhooks,
and test error scenarios without spending money.

**Test cards**:
```
4242 4242 4242 4242  → Always succeeds
4000 0000 0000 9995  → Always declines (insufficient funds)
4000 0000 0000 3220  → Requires 3D Secure authentication
```

**Best for**: Capstone C1 (therapy platform — session payment, cancellation refund policy), Lab 03 (REST API design — add a payment endpoint and handle Stripe webhooks)

---

## Cloud Storage

### Backblaze B2
**URL**: https://www.backblaze.com/docs/cloud-storage
**Auth**: Application key (free tier: 10GB storage, 1GB/day download)
**S3-compatible**: Yes — use any S3 SDK with B2 endpoint

```
# Works with standard S3 SDKs:
const s3 = new S3Client({
  endpoint: 'https://s3.us-west-004.backblazeb2.com',
  region: 'us-west-004',
  credentials: { accessKeyId: '...', secretAccessKey: '...' }
})
```

**Best for**: Lab 17 (Bash — `pg_dump | gzip | aws s3 cp` using B2's S3 endpoint), Lab 23 (Pi backup script → cloud), Capstone C1 (session report attachments)

### Cloudflare R2
**URL**: https://developers.cloudflare.com/r2/
**Auth**: API token (free tier: 10GB storage, no egress fees)
**S3-compatible**: Yes

**Best for**: Same use cases as B2. R2 has no egress fees, which is better
if you're serving files to users (e.g., media in the homelab). B2 is simpler
to set up.

**Why not AWS S3 directly**: S3 has a free tier (5GB for 12 months) but
requires a credit card and can generate unexpected charges. B2 and R2 are
genuinely free for lab-scale usage with no billing surprises.

---

## Communication Services

### Resend (Email)
**URL**: https://resend.com/docs
**Auth**: API key (free tier: 100 emails/day, 3000/month)

```
POST /emails
{
  "from": "Losângela <appointments@yourdomain.com>",
  "to": ["client@email.com"],
  "subject": "Appointment reminder — tomorrow at 14:00",
  "html": "<p>Your session is confirmed for tomorrow.</p>"
}
```

**Why Resend over SendGrid/Mailgun**: Developer experience. The API is clean,
TypeScript SDK is first-class, and the free tier is enough for a real therapy
practice. It's also what modern startups use — relevant for international roles.

**Best for**: Capstone C1 (appointment reminders, confirmation emails), Lab 19 (n8n workflow — form → email)

### Twilio (SMS)
**URL**: https://www.twilio.com/docs/sms
**Auth**: Account SID + Auth Token (free trial with $15 credit, no credit card for trial)

```
POST /2010-04-01/Accounts/{sid}/Messages.json
  From: +1234567890 (Twilio number)
  To: +5561999999999
  Body: "Your session with Losângela is tomorrow at 14:00."
```

**Best for**: Capstone C1 (SMS reminders as a WhatsApp fallback). SMS is
less common in Brazil (WhatsApp dominates), but understanding Twilio's API
is valuable for international roles where SMS is the primary channel.

### Z-API / Evolution API (WhatsApp)
**URL**: https://www.z-api.io / https://doc.evolution-api.com
**Auth**: Instance token (Z-API has a free trial; Evolution is self-hosted and free)

**Why these exist**: WhatsApp has no free public API. Meta's official Business
API requires a business account and approval. Z-API and Evolution are
Brazilian-built bridges that let you send WhatsApp messages programmatically.

**Best for**: Capstone C1 (primary reminder channel — WhatsApp is how
Brazilians actually communicate), Lab 19 (n8n workflow → WhatsApp notification)

---

## Developer Tools

### GitHub REST API
**URL**: https://api.github.com  
**Auth**: Personal token (public data is unauthed, higher limits with token)  

```
GET /repos/{owner}/{repo}/commits           → Commit history
GET /repos/{owner}/{repo}/contributors      → Contributor stats  
GET /users/{username}/repos                 → User's repositories
GET /search/repositories?q={query}         → Search repositories
```

**Best for**: Lab 19 (n8n — GitHub webhook → WhatsApp), Lab 12 (Kafka — stream your own commit events as a data source)

### JSONPlaceholder
**URL**: https://jsonplaceholder.typicode.com  
**Auth**: None  

Fake REST API for testing. Returns consistent fake data for `posts`, `comments`, `users`, `todos`.

**Best for**: Lab 00-A (mock HTTP calls in tests), Lab 02 (Result error handling with a real HTTP call)

---

## Health & Science

### OpenFDA
**URL**: https://api.fda.gov  
**Auth**: None (500 req/day), with key: 120,000 req/day  

```
GET /drug/event.json?search=patient.drug.medicinalproduct:aspirin&limit=10
  → Drug adverse event reports
GET /food/enforcement.json?search=status:ongoing  
  → Active food recalls
```

**Best for**: Lab 08 (PostgreSQL — complex queries over medical event data), Lab 10 (MongoDB — flexible schema for drug data)

---

## Lab Integration Map

| Lab | Recommended API | Why |
|---|---|---|
| **00-A** Testing | ViaCEP, BrasilAPI/CEP | Replace hardcoded test data with real CEP/CNPJ lookup; mock the API in tests to learn HTTP mocking |
| **01** TS Types | REST Countries | Complex nested object with 40+ fields — great for modeling with TypeScript generics |
| **03** REST API | BrasilAPI + Open-Meteo | Build an API that aggregates: fetch city data from IBGE, current weather from Open-Meteo, return a combined response |
| **06** Grafana | BACEN (Selic rate) | Stream daily economic indicators into Prometheus as a custom metric — more interesting than synthetic data |
| **08** PostgreSQL | IBGE municipalities | Import all 5570 Brazilian cities with state, region, population — real dataset for index optimization |
| **09** Redis | Frankfurter (exchange rates) | TTL cache makes real sense: rates update hourly, cache for 55 min |
| **10** MongoDB | Open Library | Books have truly variable schemas (different fields per edition) — perfect document model case |
| **11** RabbitMQ | OpenFDA recalls | Poll for new food/drug recalls every minute, publish to queue, consumer sends alerts |
| **12** Kafka | BACEN + GitHub | Produce events: exchange rate changes + your own commits; two different consumer groups |
| **19** n8n | GitHub API + BrasilAPI | GitHub PR webhook, enrich with contributor data; also: CPF/CNPJ lookup workflow |
| **20** AI API | Open-Meteo | Give the AI real Brasília weather context; user asks "should I bring an umbrella?" and AI has actual data |
| **24** Arduino | Open-Meteo | Compare Arduino DHT22 reading against official Brasília weather — surfaces sensor calibration issues |
| **17** Bash scripts | Backblaze B2 | `pg_dump \| gzip \| aws s3 cp` — the backup script now has a real destination |
| **23** Raspberry Pi | Backblaze B2 / R2 | Backup script → cloud storage via cron |
| **C1** Therapy app | BrasilAPI/holiday + Stripe + Resend + Twilio + Z-API | Holidays for auto-blocking, payments for sessions, email/SMS/WhatsApp for reminders |
