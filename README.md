# OECD How's Life? — Country Data Submission Portal

A web-based portal that mirrors the OECD Well-being and Time Use Questionnaire, allowing OECD member country delegates to access their profile, review pre-filled backfill data, and submit new values by indicator.

## Live Site

Once deployed, your portal will be available at:
```
https://<your-username>.github.io/<your-repo-name>/
```

---

## Features

- **Country login** — Select from all 38 OECD member countries
- **Dashboard** — All 17 indicators grouped by dimension with live completion tracking
- **Section 1** — Metadata form (source, contact, frequency, breaks in series, TUS-specific fields)
- **Section 2** — Time-series data table (2004–2024) with value + flag columns for all subgroups
- **Backfill data** — Pre-filled values shown in green; editable by the country delegate
- **Fully offline** — No backend required; all data stored in browser session

---

## Deploying to GitHub Pages

### Option A — GitHub Web UI (easiest)

1. Create a new repository on [github.com](https://github.com) (e.g. `oecd-portal`)
2. Click **Add file → Upload files**
3. Drag and drop `index.html`, `README.md`, and `.nojekyll` into the upload area
4. Click **Commit changes**
5. Go to **Settings → Pages**
6. Under **Source**, select `Deploy from a branch` → `main` → `/ (root)`
7. Click **Save** — your site will be live in ~1 minute

### Option B — Git CLI

```bash
# 1. Clone or create your repo
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>

# 2. Copy the portal files into the repo folder
cp path/to/index.html .
cp path/to/.nojekyll .

# 3. Push to GitHub
git add .
git commit -m "Add OECD well-being portal"
git push origin main

# 4. Enable GitHub Pages in Settings → Pages → Source: main / root
```

---

## Adding Backfill Data

Open `index.html` and find the `PREFILL` constant near the top of the `<script>` section:

```javascript
const PREFILL = {
  MEX: {
    '11_1': {
      s2: {
        'Country average|Country average': { 2021: 8.449 }
      }
    }
  }
};
```

### Structure

```
PREFILL[COUNTRY_CODE][INDICATOR_CODE].s2[ROW_KEY][YEAR] = value
```

- **`COUNTRY_CODE`** — 3-letter ISO code (e.g. `MEX`, `FRA`, `DEU`)
- **`INDICATOR_CODE`** — as per the questionnaire (e.g. `'1_5'`, `'11_1'`)
- **`ROW_KEY`** — `"<Indicator group>|<Indicator subgroup>"` (see table below)
- **`YEAR`** — integer from 2004 to 2024

### Common Row Keys

| Row Key | Description |
|---|---|
| `Country average\|Country average` | Main country-level value |
| `Vertical inequality\|Bottom 20%` | Bottom quintile |
| `Vertical inequality\|Top 20%` | Top quintile |
| `Horizontal inequality by sex\|Female` | Female subgroup |
| `Horizontal inequality by sex\|Male` | Male subgroup |
| `Horizontal inequality by age\|Young (15–24)` | Youth (varies by indicator) |
| `Horizontal inequality by education\|Tertiary (ISCED 5–8)` | Tertiary education |

### Example — adding France data

```javascript
const PREFILL = {
  MEX: { /* ... existing ... */ },
  FRA: {
    '11_1': {
      s2: {
        'Country average|Country average': { 2019: 6.7, 2020: 6.4, 2021: 6.5 },
        'Horizontal inequality by sex|Female': { 2021: 6.4 },
        'Horizontal inequality by sex|Male':   { 2021: 6.6 },
      }
    },
    '14_1': {
      s2: {
        'Country average|Country average': { 2021: 5.1 }
      }
    }
  }
};
```

---

## Indicators Covered

| Code | Label | Dimension |
|------|-------|-----------|
| 1_5  | Difficulty making ends meet | Income and wealth |
| 2_9  | Job satisfaction | Work and Job Quality |
| 3_5  | Inability to keep home adequately warm | Housing |
| 4_1  | Time off | Work-Life Balance |
| 4_2  | Long unpaid working hours | Work-Life Balance |
| 4_3  | Gender gap in hours worked | Work-Life Balance |
| 4_4  | Satisfaction with time use | Work-Life Balance |
| 5_4  | Risk of depression | Health |
| 5_5  | Risk of anxiety | Health |
| 7_2  | Social interactions | Social Connections |
| 7_3  | Satisfaction with personal relationships | Social Connections |
| 7_4  | Loneliness | Social Connections |
| 8_2  | Voter turnout | Civic Engagement |
| 9_1  | Access to green space | Environmental Quality |
| 11_1 | Life satisfaction | Subjective Well-being |
| 14_1 | Trust in others | Social Capital |
| 14_2 | Trust in the police | Social Capital |

---

## Notes

- Data entered in the portal is held in the browser session only (no server). For persistent storage across users, a backend (e.g. Google Sheets API, Supabase, or Airtable) would need to be wired in.
- The `.nojekyll` file tells GitHub Pages not to process the site with Jekyll, ensuring the HTML is served as-is.
