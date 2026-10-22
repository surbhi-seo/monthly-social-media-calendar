# Social media calendar config

Save a copy of this file as `social-calendar.config.md` in your working folder (or attach it
to the conversation, or paste it into a scheduled task's prompt). Only `brand_name` and
`website` are required; delete any line you don't need and the skill will use its default
or work it out from your website.

```yaml
brand_name: Acme Robotics
website: https://www.example.com

# History (optional but recommended): a Google Sheet link, a Drive folder,
# or the names of attached CSV / XLSX files with earlier months.
past_calendars:

# Where the new month goes: new-tab | new-sheet | xlsx
output: xlsx
tab_name_format: "<Mon> <YYYY>"      # e.g. Nov 2026

# Scheduling
timezone: Asia/Kolkata
posting_time: "17:00"
working_days: Mon-Fri
preferred_days: Tue, Thu, Fri
posts_per_month: 10-12
holidays:                            # dates never to post on, e.g. 2026-12-25

# Audience and voice
platforms: LinkedIn, Facebook, Instagram, X (Twitter)
audiences: Plant managers, Operations heads, Procurement leads
industries: Automotive, Food processing, Pharma
regions: India, UAE
special_days: World Quality Day, Diwali, the brand's trade shows
tone: Friendly, expert, no jargon, short sentences
brand_hashtag: "#AcmeRobotics"
contact_cta: "Book a demo: sales@example.com"
language: English

# Optional: your own column layout (otherwise past calendars or the default are used)
columns:
```
