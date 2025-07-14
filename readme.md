# 📦 PNpilot v1 — Aerospace Sourcing Automation Tool

---

## 🧩 Project Overview

PNpilot is a simple but powerful automation bot that helps aerospace sourcing by:

- Logging into key platforms like Partbase, ILS, etc.  
- Scraping **Buyer Offers (BO)**  
- Searching each BO part number in **Supplier sections of *all* platforms**  
- Matching data (part number, description, etc.) from different sources  
- Storing results into a unified CSV file  
- (Future) Sending RFQ emails from PNpilot  

---

## 🛠 Updated PNpilot Logic (Cross-Market Search)

1. **User starts PNpilot**
2. PNpilot logs into each site (using `.env` credentials)
3. Scrapes **Buyer Offers (BO)** from all platforms
4. Merges them into a unified list (called `X`)
5. For each part number in `X`, PNpilot searches **Supplier Sections of ALL platforms**
6. Matches found (based on part number or fuzzy logic) are extracted:
   - Part number
   - Description
   - Country
   - Email
   - Supplier name
   - RFQ link (if available)
7. Appends results to a **central CSV**
8. (Future) User can auto-send RFQ/email

---

## 🎯 Goal: Keep PNpilot Simple, Robust & Effective

- Minimal but production-grade tool
- Easy to maintain and extend
- Modular scraping + matching + saving system
- Simple deployment and configuration

---

# Step-by-Step Guide: Technologies & Learning Path

---

## 1. Python (Core Language)

### Why Python?

- Easy to learn, powerful for scripting & automation  
- Rich ecosystem for scraping, data handling, and web APIs  

### What to Focus On?

- **Advanced basics**: Functions, classes, exceptions  
- **HTTP & Web scraping**: `requests` library (for simple GET/POST)  
- **Concurrency**: `threading` or `asyncio` basics to speed up scraping  
- **File handling**: reading/writing JSON, CSV  
- **Libraries**:  
  - `requests` for HTTP  
  - `python-dotenv` to load config from `.env` safely  
  - `fuzzywuzzy` for string matching  

### Recommended Next Step

- Learn **Scrapy** framework for powerful scalable scraping  
- It allows structured spiders, easy to maintain  
- Start with simple Scrapy tutorials after mastering `requests`

### When to Use?

- Use **plain Python + requests** for quick scripts and login/scrape steps  
- Use **Scrapy** when you want to scale scraping, handle complex site navigation or many pages  

---

## 2. FastAPI (For API & Future UI)

### Why FastAPI?

- Fast, asynchronous, modern web framework for building APIs  
- Great for exposing PNpilot functionality via HTTP  
- Easy to add interactive dashboards or remote control  

### What to Learn?

- Basics of FastAPI routing & endpoints  
- How to handle async functions and background tasks  
- Integrating database models with Pydantic schemas  

### When to Use?

- Once your core scraping and matching work, use FastAPI to provide an interface (API or web UI)  
- Useful when multiple users or automation need to interact with PNpilot  

---

## 3. PostgreSQL + SQLAlchemy (Data Storage)

### Why PostgreSQL?

- Reliable, production-grade database  
- Great with large datasets, complex queries  

### Why SQLAlchemy?

- Python ORM that maps Python classes to DB tables  
- Easier and safer than raw SQL  

### What to Learn?

- How to define models (tables) with SQLAlchemy ORM  
- Basic CRUD operations: create, read, update, delete  
- How to connect SQLAlchemy to PostgreSQL  
- Session management and transactions  

### When to Use?

- Use once you need persistent storage of scraped data, matches, and logs  
- Critical to keep data safe, query efficiently, and scale users  

---

## 4. Celery + Redis (Background Task Queue)

### Why?

- Scraping and matching can take time and block UI  
- Celery allows running these as background jobs  
- Redis acts as message broker  

### What to Learn?

- Basics of Celery tasks  
- How to setup Redis as broker  
- Triggering tasks from FastAPI or CLI  
- Monitoring task status  

### When to Use?

- When your scraping or matching grows beyond simple scripts and needs async processing  
- Needed for handling many users or large data loads without blocking  

---

## 5. Docker (Containerization)

### Why?

- Package PNpilot with all dependencies  
- Easy deployment and scaling  
- Avoid "works on my machine" problems  

### What to Learn?

- Write a Dockerfile  
- Build and run containers locally  
- Basics of docker-compose for multi-service apps (API + DB + Redis)  

### When to Use?

- When you want consistent environment for development and deployment  
- Essential for team development or cloud hosting  

---

## 6. Anti-Blocking & Scaling Techniques

- **Use .env for credentials & proxies**: keep sensitive info safe  
- **Session reuse**: avoid re-login for every request  
- **Randomize User-Agent headers**  
- **Add delays and rate limits in scraping**  
- **Use rotating proxies or VPNs** for IP diversity  
- **Monitor for captchas & block pages** (skip or retry later)  
- **Limit concurrent threads per site** (3-5 max)  
- **Log everything** for troubleshooting  

---

## 🗺 PNpilot System Architecture & Flow

```mermaid
graph TD
    A[User runs PNpilot] --> B[Login to platforms (Partbase, ILS, etc.)]
    B --> C[Scrape Buyer Offers (BO)]
    C --> D[Scrape Supplier Offers from all platforms]
    D --> E[Match Buyer & Supplier Offers]
    E --> F[Store matches in PostgreSQL + CSV]
    F --> G{User choice}
    G -->|Export Data| H[CSV export / dashboard display]
    G -->|Send RFQ Email (Future)| I[Send emails via SMTP]
```
---

## 🔧 Technologies Overview

| Skill                        | Purpose                                                                 |
|-----------------------------|-------------------------------------------------------------------------|
| Python (requests, threading, asyncio) | Base logic, session handling, scraping                             |
| Scrapy                      | Structured scraping, handles complex sites                             |
| FastAPI                     | Optional lightweight API layer or dashboard                            |
| PostgreSQL + SQLAlchemy     | Persistent storage for buyer/supplier offers                           |
| Celery + Redis              | Background scraping + email notification (future)                      |
| Docker                      | Isolated, shareable, deployable environment                            |
| Rotating proxies, headers   | Prevent blocks when scraping                                           |
| .env + python-dotenv        | Safe storage of credentials and secrets                                |

---

## 🧠 How to Combine the Tech

| Component               | Technology            | Role                                  | When to Use                                   |
|------------------------|-----------------------|---------------------------------------|-----------------------------------------------|
| Config & Secrets       | `.env`, `python-dotenv` | Login credentials, URLs                | Always                                        |
| Buyer Scraping         | `requests`, `Scrapy`   | Get part numbers from all sources     | Step 1-2                                      |
| Supplier Matching      | `Scrapy`, `fuzzywuzzy` | Search ALL platforms for each part    | Step 3-4                                      |
| Concurrency            | `threading`, `asyncio` | Handle parallel scraping               | Step 3-4                                      |
| Storage                | PostgreSQL + SQLAlchemy | Save & query matches                 | Step 5                                        |
| Queue Tasks            | Celery + Redis         | Future: background scraping & emails  | Once scaling                                  |
| App Interface (optional)| FastAPI               | Serve dashboard or expose scraping API| For team or remote management                 |
| Deployment             | Docker                 | Easy run and deploy on cloud/local     | Always                                        |

---

## 📅 Suggested Learning Timeline (7 Weeks)

| Week | Focus Area                          | Goal                                              |
|------|-------------------------------------|---------------------------------------------------|
| 1    | Python, requests, dotenv            | Scrape buyer offers with login                    |
| 2    | SQLAlchemy + PostgreSQL             | Store and query offers                            |
| 3    | Part search + fuzzy matching        | Match buyers with supplier offers                 |
| 4    | Scrapy framework basics             | Scale scraping & support navigation, AJAX, etc.   |
| 5    | Celery + Redis                      | Schedule heavy tasks + background queueing        |
| 6    | FastAPI basics                      | Add control panel (optional)                      |
| 7    | Docker                              | Make PNpilot portable & production-ready          |

---

## 🧩 PNpilot Architecture

```mermaid
graph TD
    A[User runs PNpilot] --> B[Login to Partbase, ILS, etc.]
    B --> C[Scrape Buyer Offers (BO)]
    C --> D[Loop: For each BO → Search in all suppliers]
    D --> E[Find Match → Part#, Email, Country, Supplier, RFQ]
    E --> F[Store in Central CSV + DB]
    F --> G{User Action}
    G -->|Download CSV| H[Exported Results]
    G -->|Future: Email RFQ| I[SMTP sender / form auto-fill]
```

---

## 🔄 Example Scraping Logic

```python
from dotenv import load_dotenv
import requests, os

load_dotenv()

# Login info
LOGIN_URL = os.getenv("PARTBASE_LOGIN_URL")
USERNAME = os.getenv("PARTBASE_USER")
PASSWORD = os.getenv("PARTBASE_PASS")

session = requests.Session()
res = session.post(LOGIN_URL, data={"email": USERNAME, "password": PASSWORD})

# Example: scrape buyer offers
response = session.get("https://example.com/buyer-offers")
offers = response.json()
```

---

## 🔁 Cross-Supplier Matching Logic (Concept)

```python
buyer_offers = load_buyer_offers()

all_matches = []
for offer in buyer_offers:
    part = offer["part_number"]
    # Search all platforms
    matches = []
    matches.extend(search_partbase_supplier(part))
    matches.extend(search_ils_supplier(part))
    all_matches.extend(matches)

save_matches_to_csv(all_matches)
```

---

## 🎯 Fuzzy Match Example

```python
from fuzzywuzzy import process

part = "ABC123"
supplier_parts = ["ABC123A", "ABX123", "XYZ789"]
best_match, score = process.extractOne(part, supplier_parts)
print(best_match, score)
```

---

## 🔄 Celery Background Job Example

```python
from celery import Celery
app = Celery('tasks', broker='redis://localhost:6379/0')

@app.task
def full_scrape():
    # Login, scrape, search, save results
    return "All done"
```

Run:
```bash
celery -A tasks worker --loglevel=info
```

---

## ⚡ FastAPI Example

```python
from fastapi import FastAPI
app = FastAPI()

@app.get("/")
def home():
    return {"status": "PNpilot running"}

@app.post("/scrape")
def start_scrape():
    # trigger scrape task
    return {"message": "Scraping started"}
```

---

## 📦 Dockerfile Sample

```dockerfile
FROM python:3.10-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---



## 🔐 Example `.env` File

```dotenv
PARTBASE_LOGIN_URL=https://partbase.com/login
PARTBASE_USER=your_email
PARTBASE_PASS=your_password

ILS_LOGIN_URL=https://ils.com/login
ILS_USER=your_email
ILS_PASS=your_password
```

---

### ✅ Done Right:
- Login once, reuse session  
- Search suppliers for each BO across *all platforms*  
- Match, clean, deduplicate  
- Save unified results  
- Notify user
  
---

## 📌 Tips & Ideas
- Using PLaywrite login and save the cookies in json file and scrapy to use cookies and scrap the data (https://chatgpt.com/share/68730e36-098c-8011-87ee-794b6c4926d0)
- Add a Stock.txt to loop on to see if the buyers offers matching it so you can easily sell to them the parts in Stock
- Create a login add form so the user can add the login and passowrd of the websites that requiering the login (take the login and password then convert it to .env for each user)
- Or create a login and password for the bot and the bot login, scraping data, match it, and give it to you as csv (automate for scraping everyday)
- After scraping we can generatre an email script so the bot will notify us that its done scraping and check the csv (https://www.youtube.com/watch?v=VztRqRXeyn0, https://github.com/jhnwr/whiskey-cronjob/blob/main/new-whisky.py) (https://www.youtube.com/watch?v=q1GDSHhaH0E)
- UI will be useless if we upload the bot to cloud and setup it to run daily at specific time (calcule the time for the bot to start based on the hours of work like every 12h instead of every 24h)
-  Technologies will be useless now (FastAPI, Celery + Redis, SteamlitUi, )
- Saving files/results:
- 
| You want to...                                   | Use this format        |
| ------------------------------------------------ | ---------------------- |
| Email yourself daily with simple summary         | ✅ **CSV**              |
| Later expose this data via API (e.g., FastAPI)   | ✅ **JSON**             |
| Store everything for later search, stats, audits | ✅ **PostgreSQL (SQL)** |

- To avoid overscraping filter the outcome of data by date for like a week (like filter data for this week not for the previous one)
- Find a way to delete the output files in modify time to avoid larging the storage

---
© 2025 PNpilot Project — built with ❤️ by Reda
