---
name: monthly-social-media-calendar
description: Generate next month's social media content calendar (10-12 weekday posts with copy, captions, artwork briefs, CTAs and hashtags) for any brand, using its live website for products and its past calendars to avoid repeats. Use when asked for a monthly social media calendar or content plan, or on a monthly schedule.
---

# Monthly Social Media Calendar

Builds a complete social media calendar for the **next** month for any brand: posting dates, personas, topics, on-artwork copy, captions, artwork ideas, CTAs and hashtags, in a spreadsheet-ready structure.

It works for any website. Everything brand-specific comes from a **config** (step 0), the brand's **live website** (step 2) and its **past calendars** (step 3).

## 0. Load the config

Look for brand settings in this order:
1. Settings given in the conversation or in the scheduled task's prompt.
2. A file named `social-calendar.config.md` in the working folder or attached to the conversation (template: `config-template.md` next to this skill).

Settings (defaults in brackets):

| Setting | Meaning |
|---|---|
| `brand_name` | Brand / company name (required) |
| `website` | Main website URL, the only source for products and links (required) |
| `past_calendars` | Google Sheet link, Drive folder, or attached files with earlier months (optional but recommended) |
| `output` | `new-tab` in the past-calendars sheet, `new-sheet`, or `xlsx` [`xlsx`] |
| `tab_name_format` | Tab / file name for a month [`<Mon> <YYYY>`, e.g. `Nov 2026`] |
| `timezone` | Timezone for posting times [`UTC`] |
| `posting_time` | Default posting time [`17:00`] |
| `working_days` | Days that can carry posts [`Mon-Fri`] |
| `preferred_days` | Days to favour [`Tue, Thu, Fri`] |
| `posts_per_month` | Range [`10-12`, scaled by working days] |
| `platforms` | Platforms listed on every post [`LinkedIn, Facebook, Instagram, X (Twitter)`] |
| `audiences` | Target roles / personas |
| `industries` | Industries to rotate through |
| `regions` | Markets, used for special days and examples |
| `special_days` | Holidays, awareness days and events to consider |
| `holidays` | Dates never to post on |
| `tone` | Brand voice notes |
| `brand_hashtag` | Hashtag that ends every post's hashtag list |
| `contact_cta` | Contact line for sales CTAs (e.g. email or demo link) |
| `columns` | Column list, if the brand uses its own layout [default list in step 7] |

If `brand_name` or `website` is missing: ask the user once. If nobody is there to answer (scheduled run), stop and report what's missing; don't guess a brand.

## 1. Target month

Target = the month after today (a run on the 25th plans next month). Name it with `tab_name_format`. If that tab or file already exists and has content, stop and report instead of overwriting.

## 2. Build the current product list from the website (mandatory, every run)

Product names change, new products appear and old ones are retired. **The live website is the only source of truth for products, specs and URLs.** Past calendars are never a source for product information.

1. Fetch the homepage, the products / services / solutions menu, every category page and the product pages they link to. Also fetch blog, case-study, industries and about pages if they exist.
2. Make a working list: `name (exactly as on the site) | category | URL | 3-5 key facts from that page`.
3. Only products on this list may appear in posts, under their current names.
4. Products in past calendars that are no longer on the site are not used.
5. Products on the site that never appeared in past calendars are **new**: feature 1-2 of them this month.
6. Facts and claims come only from the product's own page. Use only URLs you opened. If a link can't be confirmed, write `[confirm exact URL before publishing]` and add a Remark.
7. If the website can't be reached, don't fall back to old calendar product names: use awareness posts instead and say so in the summary.

## 3. Read past calendars (history only)

If `past_calendars` is set, read it:
- Google Sheet: use the Google Sheets connector (`get_values`) if available; otherwise export with Google Drive `download_file_content` as .xlsx and parse it (Drive's `read_file_content` only returns a sample of a sheet). Load the `google-workspace` skill first if it exists.
- Attached CSV / XLSX files: read them directly.

From them:
- **Last 3 months:** list topics, products and post types used, so nothing repeats within 3 months.
- **Same month last year:** recurring events and special days.
- **Format and tone:** match how this brand writes copy, captions and artwork notes.

If there are no past calendars, carry on and say so in the summary.

## 4. Special days and events

For the target month, check `special_days` plus anything relevant to `regions`, `industries` and `audiences` (national days, religious festivals, industry awareness days, trade shows the brand attends). Confirm this year's dates with a web search. Use at most 1-2 per month unless an event runs over several days. Add a Remark asking the team to confirm event participation.

## 5. Schedule the posts

Run the bundled script rather than doing date maths by hand:

```bash
python3 scripts/plan_dates.py 2026 11 --working Mon-Fri --preferred Tue,Thu,Fri --min 10 --max 12 --holidays 2026-12-25
```

If the script isn't available, use this:
```python
import calendar, datetime as dt
def plan(y, m, working=(0,1,2,3,4), preferred=(1,3,4), lo=10, hi=12, holidays=()):
    days=[dt.date(y,m,d) for d in range(1,calendar.monthrange(y,m)[1]+1)
          if dt.date(y,m,d).weekday() in working and dt.date(y,m,d) not in holidays]
    n = hi if len(days)>=22 else (lo+hi)//2 if len(days)>=20 else lo
    n = min(n, len(days))
    pref=[d for d in days if d.weekday() in preferred]
    pool = pref if len(pref)>=n else days
    idx = sorted({round(i*(len(pool)-1)/max(n-1,1)) for i in range(n)})
    return [pool[i] for i in idx]
```

- Default: Monday-Friday only, 22+ working days → 12 posts, 20-21 → 11, fewer → 10.
- A special day or event posts on its own date: swap it into the nearest slot. If it falls on a non-working day, use the nearest working day and add a Remark.
- Every post uses `posting_time` in `timezone`.

## 6. Content mix

| Type | Per month (12 posts) | Promotional? | Typical post types |
|---|---|---|---|
| Product Feature | 3-4 | Yes | Static Artwork, Video / Animation |
| Awareness | 3-4 | **No** | Static Artwork, Carousel, Reel, Poll |
| Knowledge Leadership | 2-3 | Light (soft link only) | Carousel, Blog Share |
| Sales | 1 | Yes | Case Study, Testimonial, Demo invite |
| Special Day / Event / News | 0-2 (replaces other slots) | No | Static Artwork, GIF |

Scale proportionally for 10-11 posts.

**Awareness posts (non-promotional)** give the audience pure value with no product names, product images or sales CTA: tips, checklists, myths vs facts, "did you know", quizzes, industry best practice. Statistics only with a named public source in Reference Link; never invent numbers. Close with a soft brand sign-off and an engagement CTA ("Save this", "Share with your team", "Comment your tip").

Other rules:
- Per month: at least 2 carousels, 1 reel or short video, 1 poll, 1 blog share (if the site has a blog), 1 case study or testimonial (only if the site has one).
- Never 3 promotional posts in a row; spread awareness posts through the month.
- Rotate product categories; never the same category in consecutive product posts.
- Prefer products not featured in the last 3 months, new products first.
- If last month's poll promised results, add a follow-up post.
- Rotate `industries`, `audiences` and `regions`.

## 7. Columns

Use `columns` from the config. If past calendars exist, copy their exact column names and order (including spelling). Otherwise use:

`S.No, Posting Date, Posting Time, Platforms, Type, Persona, Post Type, Topic, Reference Link, Copy, Caption, Artwork Ideation, CTA, Hashtags, Artwork Link, Status, Remark, Live Link, Engagement`

How to fill them:
- **Posting Date:** `Nov 3 (Tue)`.
- **Posting Time:** `posting_time`.
- **Platforms:** `platforms`.
- **Persona:** 2-3 roles from `audiences`, one per line.
- **Topic:** `Title — one-line purpose.`
- **Reference Link:** an opened website URL; `Not Required` for polls, special days and unsourced awareness posts.
- **Copy** (text on the artwork):
  - Static → quoted headline + 1-2 supporting sentences.
  - Carousel → `Slide 1: …` to `Slide N: …` (6-8 slides; promotional ones end with a brand CTA slide using `contact_cta`, awareness ones end with "Save & share").
  - Reel / Video → timestamped script `[0-3 sec: …]` with voiceover lines, 15-20 sec.
  - Poll → question + options A-D.
  - Case Study / Blog Share / Testimonial → 3-4 short lines ending "link in caption".
  - Special Day → greeting + subtle brand sign-off.
- **Caption:** hook line, blank line, 1-2 short paragraphs, blank line, CTA (with URL for promotional posts), blank line, hashtags.
- **Artwork Ideation:** `Visual: …` scene + callouts in quotes separated by ` | `.
- **CTA:** the caption's CTA.
- **Hashtags:** 5-6 tags, ending with `brand_hashtag`.
- **Artwork Link, Live Link, Engagement:** blank.
- **Status:** `Planned`.
- **Remark:** only items the team must check (unconfirmed URL, case-study facts, event participation, new product spotted).

Tone: follow `tone` and past calendars. Short, clear sentences. Never invent certifications, specs, figures, customer names or results.

## 8. Output

Per `output`:
- **`new-tab`:** add a tab named for the target month to the past-calendars sheet (Google Sheets connector), after the latest month. Header row bold and frozen, text wrapped, values written as raw text so dates aren't converted. Never edit or delete existing tabs.
- **`new-sheet`:** create a new Google Sheet named `<brand_name> - Social Media Calendar - <month>` (upload an .xlsx via Google Drive `create_file`).
- **`xlsx`** (or nothing else available): build an .xlsx with one sheet for the month (use the `xlsx` skill if present) plus a UTF-8 .csv, and send them.

End with a short summary: link or file, number of posts, promotional vs non-promotional mix, special days used, new / renamed / removed products noticed on the website, and every Remark needing action.

## 9. Self-check before delivering

- Post count is right and every date is a working day in the target month, at `posting_time`.
- Every product mentioned exists on the website today, under its current name, with an opened URL.
- 3-4 awareness posts with no product names (scaled for 10-11 posts).
- No topic or product repeats from the last 3 months.
- All columns present in order; no empty Copy, Caption, CTA or Hashtags.
- Existing tabs and files untouched.
