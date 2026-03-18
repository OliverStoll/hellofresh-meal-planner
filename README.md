# 🥦 Smart Meal Planner

![Tests](https://github.com/oliverstoll/smart-meal-planner/actions/workflows/ci.yml/badge.svg)
![Python](https://img.shields.io/badge/python-3.10--3.13-blue)
![Coverage](https://codecov.io/gh/oliverstoll/smart-meal-planner/branch/main/graph/badge.svg)

A Telegram bot that helps you plan your weekly meals, generate shopping lists, and discover personalised recipe recommendations powered by HelloFresh data and OpenAI embeddings.


---

## Features

- **Recipe suggestions** – receive a chosen number of HelloFresh recipes filtered by your personal preferences
- **Smart shopping list** – automatically aggregated, categorised, and portion-adjusted ingredient list delivered alongside your recipes
- **Weekly subscription** – opt in to receive a new meal plan every Monday
- **Favourites** – save recipes and generate meal plans from your favourites only
- **User settings** – customise portion size, diet type (all / vegetarian / vegan / high-protein), maximum cooking time, and minimum calories
- **PDF recipes** – each recipe is sent as a formatted PDF including images, ingredients, instructions, and nutritional info
- **AI recommendations** – OpenAI embeddings + cosine similarity match recipes to your taste profile
- **Data pipeline** – Selenium-based crawler ingests recipes from HelloFresh into a PostgreSQL database

---

## Tech Stack

| Layer                 | Technology                  |
|-----------------------|-----------------------------|
| Language              | Python 3.10+                |
| Bot framework         | pyTelegramBotAPI            |
| Database              | PostgreSQL via SQLAlchemy   |
| User data             | Firebase Realtime Database  |
| ML / recommendations  | OpenAI API                  |
| PDF generation        | PyMuPDF (fitz)              |
| Web scraping          | Selenium                    |
| Containerisation      | Docker                      |

---

## Prerequisites

- Python 3.10+
- A [Telegram bot token](https://core.telegram.org/bots#botfather)
- A PostgreSQL database (e.g. [Neon](https://neon.tech/) free tier)
- A [Firebase](https://firebase.google.com/) project with Realtime Database enabled
- An [OpenAI API key](https://platform.openai.com/) (for the recommendation engine)

---

## Installation

```bash
git clone https://github.com/OliverStoll/smart-meal-planner.git
cd smart-meal-planner
pip install -r requirements.txt
```

---

## Configuration

Copy `.env.example` to `.env` (or create `.env` from scratch) and fill in your credentials:

```dotenv
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
DATABASE_URL=postgresql://user:password@host/dbname
FIREBASE_REALTIME_DB_URL=https://your-project-default-rtdb.firebaseio.com
OPENAI_API_KEY=your_openai_api_key
```

---

## Running Locally

```bash
python src/telegram.py
```

The bot will start polling for messages and schedule the weekly meal-plan delivery.

---

## Running with Docker

```bash
# Build the image
docker build -t smart-meal-planner .

# Run the container (pass your .env file)
docker run --env-file .env smart-meal-planner
```

---

## Telegram Commands

| Command | Description |
|---|---|
| `/start` or `/help` | Welcome message and quick-start guide |
| `/einstellungen` or `/settings` | Open the settings menu |
| `/gerichte` | Request a number of recipe suggestions |
| `/woechentlich` | Subscribe / manage your weekly meal plan |
| `/favoriten` | Generate a meal plan from your saved favourites |

---

## Data Pipeline

To populate the database with recipes, run the crawler modules in order:

```bash
# 1. Scrape recipe links
python -m src.data_ingestion.crawler.links

# 2. Scrape recipe content
python -m src.data_ingestion.crawler.recipes

# 3. Clean and store data
python -m src.data_ingestion.cleaning
```

---

## Running Tests

```bash
pytest
```

To exclude all data ingestion tests (which are slow and require a running database), use the `slow` marker:
```bash
pytest -m "not slow"
```

Coverage is configured via `.coveragerc`. Run with coverage report:

```bash
pytest --cov=src
```

---

## Project Structure

```
smart-meal-planner/
├── src/
│   ├── config/                 # App-wide settings and paths
│   ├── data_ingestion/
│   │   └── crawler/            # HelloFresh link & recipe crawlers
│   ├── database/               # SQLAlchemy engine configuration
│   ├── messaging/
│   │   ├── callbacks/          # Handlers: settings, subscriptions, favourites
│   │   ├── bot.py              # TelegramBot entry point
│   │   ├── messaging.py        # Message formatting and sending
│   │   └── recipes.py          # Recipe filtering and sampling
│   ├── pdf/                    # PDF creation and download helpers
│   ├── recommender/            # OpenAI embedding-based recommendation engine
│   └── supermarkets/           # Supermarket product scraper
├── tests/                      # pytest test suite
├── data/                       # Runtime data directories (PDFs, thumbnails)
├── Dockerfile
├── requirements.txt
└── pytest.ini
```

---

## License

This project is provided as-is for personal and educational use.
