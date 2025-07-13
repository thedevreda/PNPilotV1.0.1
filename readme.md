# 📦 PNpilot v1 — Aerospace Sourcing Automation Tool

---

## 🧩 Project Overview

PNpilot is a simple but powerful automation bot that helps aerospace sourcing by:

- Logging into key platforms like Partbase, ILS, etc.  
- Scraping buyer requests for quotations (RFQs)  
- Scraping supplier offers and matching them against buyer RFQs  
- Providing matched results for the user to review or contact suppliers  

---

## 🛠 The PNpilot Bot Logic (Simplified)

1. **User starts PNpilot**  
2. PNpilot logs into each site (using credentials in `.env`)  
3. PNpilot scrapes **buyer offers (RFQs)** as JSON  
4. PNpilot scrapes **supplier offers** in the supplier section  
5. PNpilot matches buyer RFQs with supplier offers (using part numbers, descriptions, fuzzy matching)  
6. User can export or review matches  
7. (Future) User can send RFQ emails directly from PNpilot (under development)  

---

## 🎯 Goal: Keep PNpilot Simple, Robust & Effective

- Focus on **easy to maintain code**  
- Use **well-known, scalable tech** without overcomplicating  
- Build a solid base you can improve later  

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

# 🗺 PNpilot System Architecture & Flow

```mermaid
graph TD
    A[User runs PNpilot] --> B[Login to platforms (Partbase, ILS, etc.)]
    B --> C[Scrape Buyer Offers (RFQs)]
    C --> D[Scrape Supplier Offers]
    D --> E[Match Buyer & Supplier Offers]
    E --> F[Store matches in PostgreSQL]
    F --> G{User choice}
    G -->|Export Data| H[CSV export / dashboard display]
    G -->|Send RFQ Email (Future)| I[Send emails via SMTP]
```

---

# How to Combine the Tech

| Component               | Technology            | Role                                  | When to Use                                   |
|-------------------------|-----------------------|-------------------------------------|-----------------------------------------------|
| Configuration           | \`.env\` + \`python-dotenv\` | Store credentials & URLs             | Always, from day 1                            |
| Login & Scraping        | Python + \`requests\` / Scrapy | HTTP requests, session management    | Start with \`requests\`, scale with Scrapy      |
| Data Storage            | PostgreSQL + SQLAlchemy | Store offers & matches               | After first data scraping iteration           |
| Matching Algorithm      | Python + FuzzyWuzzy    | Fuzzy string matching for parts     | Once data is stored, to find matches           |
| Async Background Tasks  | Celery + Redis         | Run scraping & matching async       | When scaling or adding UI responsiveness       |
| API / UI (Future)       | FastAPI                | Expose PNpilot functions            | When user interaction or multi-user needed     |
| Containerization        | Docker                 | Package and deploy                   | Before deployment or team sharing               |

---

# Suggested Learning Timeline & Focus

| Week | Focus Area                           | Goal                                         |
|-------|------------------------------------|----------------------------------------------|
| 1     | Python requests, dotenv, simple scraper | Build scraper to login and get buyer RFQs   |
| 2     | SQLAlchemy & PostgreSQL setup       | Store and query buyer & supplier offers      |
| 3     | Fuzzy matching with FuzzyWuzzy      | Match buyer and supplier offers              |
| 4     | Scrapy framework basics             | Scale scraping, handle site navigation       |
| 5     | Celery + Redis for async tasks      | Offload scraping/matching from main process  |
| 6     | FastAPI basics                     | Expose bot as API, build UI                   |
| 7     | Docker basics                      | Containerize app, prep for deployment         |

---

# PNpilot Learning Checklist + Example Snippets

---

## 1. Python Core + Requests + dotenv

### Mini-Task  
- Write a script to login using \`requests.Session()\`  
- Store credentials in \`.env\` and load securely  

```python
import os
import requests
from dotenv import load_dotenv

load_dotenv()

LOGIN_URL = os.getenv("PARTBASE_LOGIN_URL")
USERNAME = os.getenv("PARTBASE_USER")
PASSWORD = os.getenv("PARTBASE_PASS")

session = requests.Session()
payload = {"username": USERNAME, "password": PASSWORD}

response = session.post(LOGIN_URL, data=payload)
if response.ok:
    print("Login successful")
else:
    print("Login failed")
```

---

## 2. PostgreSQL + SQLAlchemy Basics

### Mini-Task  
- Create models for BuyerOffer and SupplierOffer  
- Insert and query dummy data  

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import sessionmaker, declarative_base

Base = declarative_base()

class BuyerOffer(Base):
    __tablename__ = 'buyer_offers'
    id = Column(Integer, primary_key=True)
    part_number = Column(String)
    description = Column(String)

engine = create_engine('postgresql://postgres:password@localhost:5432/pnpilotdb')
Base.metadata.create_all(engine)

Session = sessionmaker(bind=engine)
session = Session()

offer = BuyerOffer(part_number="ABC123", description="Test part")
session.add(offer)
session.commit()

results = session.query(BuyerOffer).all()
for r in results:
    print(r.part_number, r.description)
```

---

## 3. Fuzzy Matching with FuzzyWuzzy

```python
from fuzzywuzzy import process

buyer_part = "ABC123"
supplier_parts = ["ABC124", "ABX123", "XYZ789"]

match, score = process.extractOne(buyer_part, supplier_parts)
print(f"Best match: {match} with score {score}")
```

---

## 4. Scrapy Framework Basics

```bash
scrapy startproject pnpilot_spider
cd pnpilot_spider
scrapy genspider example example.com
```

---

## 5. Celery + Redis Setup for Async Tasks

```python
# tasks.py
from celery import Celery

app = Celery('tasks', broker='redis://localhost:6379/0')

@app.task
def scrape_site():
    # scraping logic here
    return "Scraping done"
```

Run worker:  
```bash
celery -A tasks worker --loglevel=info
```

Call task:  
```python
scrape_site.delay()
```

---

## 6. FastAPI Basics

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/status")
def read_status():
    return {"status": "PNpilot is running"}

@app.post("/start-scrape")
def start_scrape():
    # call your scraping logic here
    return {"message": "Scraping started"}
```

Run server:  
```bash
uvicorn main:app --reload
```

---

## 7. Docker Basics

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

# Final Tips
- Using PLaywrite login and save the cookies in json file and scrapy to use cookies and scrap the data (https://chatgpt.com/share/68730e36-098c-8011-87ee-794b6c4926d0)
- Add a Stock.txt to loop on to see if the buyers offers matching it so you can easily sell to them the parts in Stock
- Create a login add form so the user can add the login and passowrd of the websites that requiering the login (take the login and password then convert it to .env for each user)
- Or create a login and password for the bot and the bot login, scraping data, match it, and give it to you as csv (automate for scraping everyday)

© 2025 PNpilot Project — built with ❤️ by Reda
