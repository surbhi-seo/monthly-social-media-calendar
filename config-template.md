# social-calendar.config.md

Copy this file into your project as `social-calendar.config.md`, fill it in, and delete lines you don't need. Only `brand_name` and `website` are required.

```yaml
brand_name: Acme Industrial
website: https://www.example.com
past_calendars: https://docs.google.com/spreadsheets/d/<your-sheet-id>   # or a Drive folder link, or leave blank
output: new-tab            # new-tab | new-sheet | xlsx
tab_name_format: "<Mon> <YYYY>"

timezone: Asia/Kolkata
posting_time: "17:00"
working_days: Mon-Fri
preferred_days: Tue, Thu, Fri
posts_per_month: 10-12
holidays: []               # e.g. [2026-12-25, 2027-01-01]

platforms: LinkedIn, Facebook, Instagram, X (Twitter), YouTube
audiences:
  - Operations Managers
  - Procurement Heads
  - Safety Officers
industries: [Manufacturing, Logistics, Oil & Gas]
regions: [India, UAE, UK]
special_days: [New Year, World Day for Safety and Health at Work]

tone: Confident, practical, short sentences, no hype.
brand_hashtag: "#AcmeIndustrial"
contact_cta: "Talk to our team: sales@example.com"

# Optional: your own column layout (otherwise copied from past calendars, or the skill default)
columns: []
```
