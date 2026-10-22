# Monthly Social Media Calendar — a Claude skill

Plans next month's social media posts for any brand: 10-12 posting dates with personas, topics, on-artwork copy, captions, artwork briefs, CTAs and hashtags, delivered as an Excel/CSV file or a new tab in your Google Sheet.

It reads your **live website** every time (so product names and links are always current) and, if you share them, your **past calendars** (so topics don't repeat and the tone matches).

## Install

- **Claude.ai / Claude desktop:** Settings → Capabilities → Skills → upload `monthly-social-media-calendar.skill` (or the zipped folder).
- **Claude Code:** copy the `monthly-social-media-calendar` folder into `~/.claude/skills/` (personal) or `.claude/skills/` in a project.
- **Claude API:** upload the folder through the Skills API.

## Use

Just ask, for example:

> Make next month's social media calendar for Acme Robotics, website https://www.example.com

Claude asks only for what it can't find. For best results, also share:
- past calendars (a Google Sheet link or attached CSV/XLSX),
- platforms, target audience, and any dates you never post on.

To skip the questions every month, fill in `config-template.md`, save it as `social-calendar.config.md` and attach it (or keep it in your working folder).

## Run it automatically every month

Ask Claude to set up a monthly scheduled task, and include your brand name and website (or the config) in the task, since scheduled runs can't ask questions.

## Optional connectors

- **Google Sheets / Google Drive** — read past calendars and write the new month straight into your sheet.
- Without them, the skill outputs an .xlsx and .csv you can upload anywhere.

## What it won't do

It never invents statistics, certifications, customer names or results, and only links pages it has actually opened. Anything the team should double-check is listed in the **Remark** column.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Instructions Claude follows |
| `config-template.md` | Reusable brand settings |
| `scripts/plan_dates.py` | Picks posting dates (Python 3, no extra packages) |

## License

MIT — see `LICENSE`.
