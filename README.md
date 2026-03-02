# Web Scraping Toolkit

This toolkit includes a variety of workflows to help you gather content from webpages and social media. The toolkit is in progress. 
---

This project includes:
- API-based collection workflows  
- HTML scraping utilities  
- An optional login-based NYT scraper using Playwright  
- An automated test suite (using `pytest`)  

> Always review a website’s Terms of Service before scraping.

---

# 1. Installation

## 1.1 Requirements

- Python **3.8+**
- macOS, Linux, or Windows
- `pip` (Python package manager)

## 1.2 Clone the repository

```bash
git clone https://github.com/your-username/Web-Scraping-Toolkit.git
cd Web-Scraping-Toolkit
```

## 1.3 Install dependencies

From the project root:

```bash
python3 -m pip install -r requirements.txt
```

This installs:

- `requests`, `beautifulsoup4`, `lxml` – HTTP requests and HTML parsing  
- `playwright`, `python-dotenv` – required only for the login-based NYT scraper  
- `pytest` – for running automated tests  

If you plan to use the login-based NYT scraper, you must also install a browser:

```bash
python3 -m playwright install chromium
```

---

# 2. New York Times Workflow

There are two approaches:

- **Option A** – Basic scraper (no login)
- **Option B** – Login-based scraper (requires NYT account)

For detailed documentation, see:
- `New York Times Scraping.md`

---

## 2.1 Step 1 – Get NYT Article URLs (API)

1. Obtain a New York Times API key.
2. Save it in a file called:

```
nyt_key.txt
```

3. Use `nyt_archives_api.py`.
4. Edit the script to set:
   - Year and month
   - Search terms

Run:

```bash
python3 nyt_archives_api.py
```

This generates a file like:

```
nyt_extracts_1.csv
```

This file contains article metadata and URLs.

---

## 2.2 Step 2 – Create `nytlinks.csv`

1. Open `nyt_extracts_*.csv`.
2. Copy the **URL column**.
3. Paste into a new one-column spreadsheet.
4. Save as:

```
nytlinks.csv
```

Place this file in the same folder where you’ll run your scraper.

---

## 2.3 Option A – Basic Scraper (`nytscraper.py`)

Uses:
- `requests`
- `BeautifulSoup`

Best for:
- Demonstrations
- Pages that are mostly static

### Run:

Make sure the folder contains:
- `nytlinks.csv`
- `nytscraper.py`

Then run:

```bash
python3 nytscraper.py
```

Output:

```
articlefulltext.csv
```

Columns:
- Date (parsed from URL)
- Article URL
- Article Full Text

If NYT serves a JS/paywall placeholder, the script records:

```
[UNAVAILABLE: page requires JavaScript / paywall; full text not scraped]
```

---

## 2.4 Option B – Login-Based Scraper (`nytscraper_login.py`)

Use this if you have:
- An NYT subscription
- A student or institutional login

This version uses **Playwright** to log in and scrape content dynamically.

### Step 1 – Install Playwright

```bash
python3 -m pip install playwright
python3 -m playwright install chromium
```

### Step 2 – Set credentials

Do not hardcode credentials in the script.

### Option A – Environment variables

Mac/Linux:

```bash
export NYT_EMAIL="your-email@example.com"
export NYT_PASSWORD="your-password"
```

Windows (PowerShell):

```powershell
setx NYT_EMAIL "your-email@example.com"
setx NYT_PASSWORD "your-password"
```

### Option B – `.env` file

Create a `.env` file in the same folder as the script:

```
NYT_EMAIL=your-email@example.com
NYT_PASSWORD=your-password
```

### Step 3 – Run

Make sure the folder contains:
- `nytlinks.csv`
- `nytscraper_login.py`

Run:

```bash
python3 nytscraper_login.py
```

The script will:
- Launch Chromium
- Log in
- Visit each article
- Extract text using multiple CSS selectors
- Write `articlefulltext.csv`

If your account uses two-factor authentication, set:

```python
headless=False
```

so you can complete login manually in a visible browser window.

---

# 3. Guardian Workflow

See:
- `Guardian Collection Workflow.md`

---

## 3.1 Step 1 – Get API Results

1. Use the Guardian Open Platform API.
2. Save the returned JSON as:

```
query_result.json
```

---

## 3.2 Step 2 – Run Scraper

Ensure folder contains:
- `query_result.json`
- `guardian_scraping.py`

Run:

```bash
python3 guardian_scraping.py
```

Output:

```
guardian_results.csv
```

Columns:
- Title
- Date (YYYY-MM-DD)
- URL
- Full text

---

# 4. Running Tests

Tests are written using `pytest`.

They:
- Do NOT hit live NYT or Guardian servers
- Mock network requests and browser calls

## Run all tests

From the project root:

```bash
pytest
```

Test coverage includes:

- `test_nytscraper.py`
  - Validates `<p>` extraction
  - Confirms paywall placeholder behavior

- `test_guardian_scraping.py`
  - Mocks API JSON + HTML requests

- `test_nytscraper_login.py`
  - Tests article extraction logic using a fake Playwright page object

If all tests pass, your environment is correctly configured.

---

# 6. Notes & Best Practices

- Avoid scraping at high frequency.
- Respect robots.txt and API rate limits.
- Use API endpoints when available instead of scraping raw HTML.
- Never commit credentials (`.env` should be in `.gitignore`).
- Consider adding logging if running large scraping jobs.

---
