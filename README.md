# bbb-scrapper

Scraper for [bbb.org](https://www.bbb.org) (Better Business Bureau). Searches companies across US cities, collects business details, and stores everything in PostgreSQL. Bypasses Cloudflare protection via cloudscraper.

## What it does

- Fetches a list of cities across the USA (500+)
- Searches companies by category in each city (default: Restaurants)
- Collects name, address, phone numbers, website, years in business, description, and ownership info
- Deduplicates on insert — safe to re-run
- Stores data in a normalized PostgreSQL schema

## Stack

Python 3.11, cloudscraper, BeautifulSoup4, psycopg2, PostgreSQL, Docker

## Quick Start

### Docker

```bash
git clone https://github.com/pavlodef/bbb-scrapper.git
cd bbb-scrapper
cp .env.example .env
docker compose up --build
```

The scraper starts automatically after Postgres is ready.

### Without Docker

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
cd scrapper
python database_create.py   # init schema
python parse.py              # run scraper
```

## Environment Variables

```env
BASE_URL=https://www.bbb.org
CITY_SEARCH_URL=/api/v1/cities
COMPANY_SEARCH_URL=/api/v1/search

DATABASE_URL=postgresql://user:password@localhost:5432/bbb_db

POSTGRES_USER=user
POSTGRES_PASSWORD=password
POSTGRES_DB=bbb_db
```

## Database Schema

```
addresses        — address, city, state, postalcode
companies        — id, name, phone[], website, years, description, address_id
personnel        — name, position
company_personnel — M:M join table
```

## Project Structure

```
bbb-scrapper/
├── scrapper/
│   ├── parse.py                # main scraper loop
│   ├── parse_cities.py         # city list fetcher
│   ├── cloudscaper_client.py   # cloudscraper setup
│   ├── database_create.py      # schema init
│   ├── database_utils.py       # db helpers
│   ├── models.py               # Company dataclass
│   └── unitests.py
├── .env.example
├── Dockerfile
├── docker-compose.yml
└── entrypoint.sh
```

## Tests

```bash
cd scrapper
pytest unitests.py -v
```

---

## Features

- Обхід Cloudflare-захисту через `cloudscraper`
- Пошук міст по всій США (до 5000+ локацій)
- Збір компаній по категоріях (рестарани, сервіси тощо)
- Деталізована інформація: адреса, телефони, сайт, власники, опис, вік компанії
- Дедублікація на рівні postgres (`ON CONFLICT DO NOTHING`)
- PostgreSQL з нормалізованою схемою (companies, addresses, personnel)
- Docker-ready

---

## Stack

| | |
|---|---|
| Runtime | Python 3.11 |
| HTTP | cloudscraper + requests |
| HTML parsing | BeautifulSoup4 |
| Database | PostgreSQL (psycopg2) |
| Tests | pytest |
| Deploy | Docker + Docker Compose |

---

## Quick Start

### Docker (рекомендовано)

```bash
git clone https://github.com/pavlodef/bbb-scrapper.git
cd bbb-scrapper
cp .env.example .env          # заповни змінні
docker compose up --build
```

Scraper запуститься автоматично після старту Postgres.

### Локально

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
cd scrapper
python parse.py
```

---

## Environment Variables

```env
BASE_URL=https://www.bbb.org
CITY_SEARCH_URL=/api/v1/cities
COMPANY_SEARCH_URL=/api/v1/search

DATABASE_URL=postgresql://user:password@localhost:5432/bbb_db

POSTGRES_USER=user
POSTGRES_PASSWORD=password
POSTGRES_DB=bbb_db
```

---

## Database Schema

```
addresses       — адреси (address, city, state, postalcode)
companies       — компанії (id, name, phone[], website, years, description)
personnel       — персонал (name, position)
company_personnel — зв'язок M:M
```

---

## Project Structure

```
bbb-scrapper/
├── scrapper/
│   ├── parse.py               # Головний парсер
│   ├── parse_cities.py        # Отримання списку міст
│   ├── cloudscaper_client.py  # Cloudflare-bypass клієнт
│   ├── database_create.py     # Ініціалізація схеми БД
│   ├── database_utils.py      # DB helpers
│   ├── models.py              # Dataclass Company
│   └── unitests.py            # Тести
├── .env.example
├── Dockerfile
├── docker-compose.yml
└── entrypoint.sh
```

---

## Tests

```bash
cd scrapper
pytest unitests.py -v
```