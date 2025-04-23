# 🎮 Game Database Project

This project includes two main parts:

---

## 🧩 1. Supabase Viewer (`index.html`)

A self-contained HTML viewer for Supabase tables and views. It allows you to:

- Browse any table or view from your Supabase database
- Search across all columns in real time
- Filter by `playtime` using operators (`=`, `>`, `<`, `>=`, `<=`) and values
- Press `Enter` after entering a playtime value to apply the filter
- Sort any column (with visual arrows and blue header highlight)
- Automatically scrolls the sorted column into view
- Choose columns to display via a toggleable checklist
- View as either a traditional table or a card layout (for mobile)
- Remember your preferred view and columns across sessions
- Paginate or view all rows at once
- Export all results to CSV with proper quoting and a timestamped filename

### How it works

- Connects using your Supabase project’s anon key (hardcoded in the HTML)
- Loads all public views via the `list_views()` RPC
- Fetches full data in batches for performance
- Defaults to sorting by `title` if available

### Features

| Feature                   | Description |
|---------------------------|-------------|
| 🔍 Search                 | Filters across all fields (case-insensitive) |
| 🎛 Column Toggle         | Show/hide any column — remembers selection per view |
| 🪄 Toggle View           | Switch between card and table views |
| ⬆️ Sortable Headers     | Click to toggle ascending/descending sort |
| ⏳ Playtime Filter       | Enter a numeric value and operator to filter results |
| 🧭 Auto-Scroll to Column | Scrolls sorted column into view |
| 💾 Export to CSV         | Downloads a clean spreadsheet of the full dataset |
| 📱 Responsive            | Card layout for mobile, table for desktop |

### Maintenance Tips

- To make views appear in the dropdown, define them in Supabase and make sure `list_views()` returns them.
- If you want default sorting on a different column than `title`, update the fallback logic in `initData()`.
- All logic is inline in the HTML file — no bundling or external libraries required.
A self-contained HTML viewer for Supabase tables and views. It allows you to:

- Browse any table or view from your Supabase database
- Search/filter across all columns
- Sort columns (with visual arrows and column highlighting)
- Control pagination and export results to CSV
- Toggle views using a dropdown (powered by a `list_views()` RPC)

### How it works

- Connects using your Supabase project’s anon key
- Calls a SQL function `list_views()` which returns public views (you must create this in Supabase)
- Pulls data in batches (for performance)
- Default sorts by `title` if available

### Key feature: Visual Sorting

The current sort column shows a blue highlight and either ▲ (ascending) or ▼ (descending).

### Maintenance Tips

- To add new views, simply create them in Supabase’s SQL Editor — they’ll appear in the dropdown.
- If your Supabase schema changes, verify column names still include `title` if you want it to default-sort properly.
- Styling and behavior is all inside the HTML — no external dependencies.

---

## 🛠 2. Game Sync (Steam Only)

Located inside the `games-database - Working - Steam Only` folder, this Node.js project:

- Fetches your game library from the Steam API
- Enriches it with metadata (e.g. RAWG)
- Pushes it to a Supabase table via REST

### Key Files

| File                             | Purpose |
|----------------------------------|---------|
| `scrapers/steam.js`              | Fetches Steam library using Steam Web API |
| `syncGames.js`                   | Main script for syncing and enriching all games |
| `sync/upsertGames.js`           | Handles UPSERT logic into Supabase |
| `utils/enrich.js`               | Queries RAWG API for genre, ratings, metadata |
| `utils/supabase.js`             | Supabase client configuration |
| `utils/logger.js`               | Logging system for sync and debug info |
| `logs/*.json/html`              | History of sync runs for troubleshooting |
| `.env`                           | Contains your Steam API key, Supabase keys, and RAWG key (not committed) |

### How to Run It

```bash
npm install
```

Then configure your `.env`:

```
SUPABASE_URL=
SUPABASE_KEY=
STEAM_API_KEY=
RAWG_API_KEY=
```

Then run:

```bash
node syncGames.js steam
```

### Maintenance Tips

- This version only supports Steam. GOG and others are under development.
- All enriched results are cached in `logs/` for debug history.
- Update RAWG queries in `enrich.js` if their API changes.

---

## ✅ Setup Checklist

- [x] Supabase `games` table created
- [x] Supabase `list_views()` RPC created:

```sql
create or replace function list_views()
returns table(name text)
language sql
as $$
  select table_name as name
  from information_schema.views
  where table_schema = 'public'
$$;
```

- [x] Environment variables set in `.env`
- [x] Steam API key obtained from [Steam Web API Key](https://steamcommunity.com/dev/apikey)

---

## 💡 Future Ideas

- Add support for GOG, Epic, PSN, etc.
- Add favoriting/tagging UI to `index.html`
- Support column hiding and column reordering
- Dark mode theme toggle (light/dark)
- Add "last updated" indicator from sync timestamps
