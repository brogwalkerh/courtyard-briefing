# Courtyard Briefing

A single-page team briefing display for the front desk at Courtyard Liberty Station.
It's built for a wall TV but also works on a phone or tablet.
All content comes live from a published Google Sheet. There is no server and no build step.

## What it shows

- **Top bar:** property name, live status, date, current shift (Night Audit / AM / PM), clock
- **Sidebar:** PMS migration countdown, today's snapshot (arrivals, departures, occupancy, VIPs),
  Act of Awe reminder, weather, and the checklist for the current shift
- **Left panel:** rotates between *Upcoming Tasks* and *Reminders*
- **Right panel:** rotates between *Weekly Updates* and *Team Shoutouts*

Long lists scroll themselves during each rotation, so nothing gets cut off on a TV.

## Running it

Open `index.html` in a browser. To host it, any static host works (GitHub Pages, Netlify, etc.).
For a TV, open it full-screen in kiosk mode.

## Google Sheet setup

1. Create a Sheet with these tabs (the names must match exactly):

   | Tab         | Columns                                                  |
   |-------------|----------------------------------------------------------|
   | `tasks`     | `title`, `meta`, `due`, `dueClass`, `priority`, `active` |
   | `reminders` | `category`, `text`, `highlight`, `active`                |
   | `updates`   | `date`, `text`, `source`, `active`                       |
   | `shoutouts` | `name`, `reason`, `active`                               |
   | `stats`     | `key`, `value`: rows `arrivals`, `departures`, `occupancy`, `vips` |
   | `weather`   | `key`, `value`: rows `temp`, `description`, `high`, `low`, `pool` |
   | `config`    | *(optional)* `key`, `value`: rows `migrationDate` (YYYY-MM-DD), `rotationInterval` (seconds), `propertyName` |

   - `priority`: `high` | `medium` | `low`
   - `dueClass`: `due-urgent` | `due-soon` | `due-normal`
   - `active` / `highlight`: `TRUE` or `FALSE` (case doesn't matter). Rows with `active = FALSE` are hidden.

2. **File → Share → Publish to web → Entire document → CSV.**
3. Put the Sheet ID (the long string in the Sheet URL) in `SHEET_ID` near the top of the `<script>` in `index.html`.

The page re-fetches every 5 minutes. The status dot in the top bar means:

- 🟢 **Live:** every tab loaded
- 🟡 **Partial / Stale:** some tabs failed, or the latest fetch failed and older data is still showing
- 🔴 **Offline:** nothing has loaded yet

> **Gotcha:** if a tab name is misspelled or missing, Google quietly returns the *first* tab instead.
> The page checks each tab's header row and ignores a tab that doesn't match, so check the browser
> console if a section stays empty.

## Editing the static content

The shift checklists (`CHECKLISTS`) and the defaults (`DEFAULT_CONFIG`) are near the top of the script in `index.html`.
