---
name: monthly-social-media-calendar
description: Plan a full month of social media posts for any brand — dates, personas, topics, on-artwork copy, captions, artwork briefs, CTAs and hashtags — as a ready-to-use spreadsheet, using the brand's live website for products and its past calendars to avoid repeats. Use this whenever someone asks for a social media calendar, monthly content plan, posting schedule, editorial calendar for LinkedIn/Instagram/Facebook/X, "next month's posts", or wants this set up as a recurring monthly task, even if they don't use the word "calendar".
---

# Monthly Social Media Calendar

Builds a complete social media calendar for the **next** month for any brand: posting dates, personas, topics, on-artwork copy, captions, artwork ideas, CTAs and hashtags, delivered as a spreadsheet (or a new tab in an existing Google Sheet).

It works for any business with a website. Everything brand-specific comes from three places: the **settings** (step 0), the brand's **live website** (step 2) and, if available, its **past calendars** (step 3). Nothing about a particular brand, country or industry is assumed.

Bundled files (in this skill's folder):
- `config-template.md` — settings template a user can fill in once and reuse every month.
- `scripts/plan_dates.py` — picks posting dates (standard-library Python only).

## 0. Settings

Look for settings in this order, later ones filling gaps in earlier ones:
1. Settings written in the conversation or in a scheduled task's prompt.
2. A `social-calendar.config.md` file in the working folder or attached to the conversation (format: `config-template.md`).
3. Defaults below, or values inferred from the website in step 2.

| Setting | Meaning | Default |
|---|---|---|
| `brand_name` | Brand / company name | **required** |
| `website` | Main website URL; the only source for products and links | **required** |
| `past_calendars` | Google Sheet link, Drive folder, or attached CSV / XLSX files with earlier months | none |
| `output` | `new-tab` (in the past-calendars sheet), `new-sheet`, or `xlsx` | `xlsx` |
| `tab_name_format` | Tab / file name for the month | `<Mon> <YYYY>` (e.g. `Nov 2026`) |
| `timezone` | Timezone for posting times | user's timezone if known, else `UTC` |
| `posting_time` | Default posting time | `17:00` |
| `working_days` | Days that can carry posts | `Mon-Fri` |
| `preferred_days` | Days to favour | `Tue, Thu, Fri` |
| `posts_per_month` | Range | `10-12` |
| `platforms` | Platforms listed on every post | `LinkedIn, Facebook, Instagram, X (Twitter)` |
| `audiences` | Target roles / personas | inferred from the website |
| `industries` | Industries to rotate through | inferred from the website |
| `regions` | Markets, for special days and examples | inferred from the website |
| `special_days` | Holidays, awareness days and events to consider | found in step 4 |
| `holidays` | Dates never to post on | none |
| `tone` | Brand voice notes | inferred from the website and past calendars |
| `brand_hashtag` | Hashtag that ends every hashtag list | `#` + brand name without spaces |
| `contact_cta` | Contact line for sales CTAs | contact page / email found on the website |
| `language` | Language of all post text | language of the website |
| `columns` | Custom column list | see step 7 |

**When `brand_name` or `website` is missing:**
- If someone is in the conversation, ask for both in one short message, and mention they can optionally share past calendars, platforms and target audience. Don't ask about every setting — the defaults and the website cover the rest, and a long questionnaire puts people off.
- If nobody can answer (a scheduled run), stop and report exactly what's missing. Never guess a brand: a calendar for the wrong company is worse than none.

At the end of a first run, offer to save the settings used as `social-calendar.config.md` so next month needs no questions.

## 1. Target month

Target = the month after today (a run on the 25th plans next month), unless the user names a month. Name it with `tab_name_format`. If a tab or file with that name already exists and has content, stop and report rather than overwrite — someone may already have edited it.

## 2. Build the current product list from the website (every run)

Product names change, new products appear and old ones are retired, so **the live website is the only source of truth for products, specs and URLs.** Past calendars are never a source of product information, because they freeze names that may since have changed.

1. Fetch the homepage, the products / services / solutions menu, every category page and the product pages they link to. Also fetch blog, case-study, industries, about and contact pages if they exist. Use whatever web-fetching tool is available; if a page is blocked, try its sitemap (`/sitemap.xml`) to find URLs.
2. Make a working list: `name (exactly as on the site) | category | URL | 3-5 key facts from that page`.
3. Only products on this list may appear in posts, under their current names.
4. Products in past calendars that are no longer on the site are not used.
5. Products on the site that never appeared in past calendars are **new**: feature 1-2 of them this month.
6. Facts and claims come only from the product's own page. Use only URLs you actually opened. If a link can't be confirmed, write `[confirm exact URL before publishing]` and add a Remark.
7. If the website can't be reached at all, don't fall back to old product names: plan awareness posts instead and say so in the summary.

While on the site, fill any inferred settings (audiences, industries, regions, tone, contact, language).

## 3. Read past calendars (history only)

If `past_calendars` is set, read it with whatever is available:
- **Google Sheet:** a Google Sheets connector (read values per tab) if connected; otherwise export the file as .xlsx through a Google Drive connector and parse it. Load a Google Workspace skill first if one is listed.
- **Attached CSV / XLSX:** read directly (use an `xlsx` skill if one is listed).
- **No access** (no connector, private link): ask the user to attach an export or connect the app; in a scheduled run, continue without history and say so.

From them:
- **Last 3 months:** topics, products and post types used, so nothing repeats within 3 months.
- **Same month last year:** recurring events and special days.
- **Format and tone:** match how this brand writes copy, captions and artwork notes, and its column layout.

If there are no past calendars, carry on and say so in the summary.

## 4. Special days and events

For the target month, check `special_days` plus anything relevant to `regions`, `industries` and `audiences` (national days, religious festivals, industry awareness days, trade shows the brand attends). Confirm this year's dates with a web search, since many shift each year. Use at most 1-2 per month unless an event runs over several days, and add a Remark asking the team to confirm event participation. Avoid days of mourning or sensitive dates for celebratory posts.

## 5. Schedule the posts

Run the bundled script rather than doing date maths by hand (hand-counted weekdays are a common source of errors):

```bash
python3 <this-skill-folder>/scripts/plan_dates.py 2026 11 \
  --working Mon-Fri --preferred Tue,Thu,Fri --min 10 --max 12 \
  --holidays 2026-12-25 --fixed 2026-11-14 --json
```

`--fixed` takes special-day dates; each replaces the nearest planned slot, and one on a non-working day moves to the nearest working day with a warning (turn the warning into a Remark).

Default count: 22+ working days → max (12), 20-21 → midpoint (11), fewer → min (10).

If Python can't run, apply the same rules by hand and double-check each weekday. Every post uses `posting_time` in `timezone`.

## 6. Content mix

| Type | Per month (12 posts) | Promotional? | Typical post types |
|---|---|---|---|
| Product Feature | 3-4 | Yes | Static Artwork, Video / Animation |
| Awareness | 3-4 | **No** | Static Artwork, Carousel, Reel, Poll |
| Knowledge Leadership | 2-3 | Light (soft link only) | Carousel, Blog Share |
| Sales | 1 | Yes | Case Study, Testimonial, Demo invite |
| Special Day / Event / News | 0-2 (replaces other slots) | No | Static Artwork, GIF |

Scale proportionally for other post counts. For a business with services rather than products, read "Product Feature" as "Service Feature".

**Awareness posts** give the audience pure value with no product names, product images or sales CTA: tips, checklists, myths vs facts, "did you know", quizzes, industry best practice. They build trust and reach, which a feed of ads doesn't. Statistics only with a named public source in Reference Link; never invent numbers. Close with a soft brand sign-off and an engagement CTA ("Save this", "Share with your team", "Comment your tip").

Other rules:
- Per month: at least 2 carousels, 1 reel or short video, 1 poll, 1 blog share (if the site has a blog), 1 case study or testimonial (only if the site has one).
- Never 3 promotional posts in a row; spread awareness posts through the month.
- Rotate product categories; never the same category in consecutive product posts.
- Prefer products not featured in the last 3 months, new products first.
- If last month's poll promised results, add a follow-up post.
- Rotate `industries`, `audiences` and `regions`.

## 7. Columns

Use `columns` if set. Otherwise, if past calendars exist, copy their exact column names and order (including spelling) so the team's sheet stays consistent. Otherwise use:

`S.No, Posting Date, Posting Time, Platforms, Type, Persona, Post Type, Topic, Reference Link, Copy, Caption, Artwork Ideation, CTA, Hashtags, Artwork Link, Status, Remark, Live Link, Engagement`

How to fill them:
- **Posting Date:** `Nov 3 (Tue)`.
- **Posting Time:** `posting_time` (add the timezone once in the summary).
- **Platforms:** `platforms`.
- **Persona:** 2-3 roles from `audiences`, one per line.
- **Topic:** `Title — one-line purpose.`
- **Reference Link:** an opened website URL; `Not Required` for polls, special days and unsourced awareness posts.
- **Copy** (text on the artwork):
  - Static → quoted headline + 1-2 supporting sentences.
  - Carousel → `Slide 1: …` to `Slide N: …` (6-8 slides; promotional ones end with a CTA slide using `contact_cta`, awareness ones end with "Save & share").
  - Reel / Video → timestamped script `[0-3 sec: …]` with voiceover lines, 15-20 sec.
  - Poll → question + 2-4 options (A-D); most platforms cap polls at 4 options.
  - Case Study / Blog Share / Testimonial → 3-4 short lines ending "link in caption".
  - Special Day → greeting + subtle brand sign-off.
- **Caption:** hook line, blank line, 1-2 short paragraphs, blank line, CTA (with URL for promotional posts), blank line, hashtags. Keep it within the shortest platform's limit if X is included (280 characters), or add a short X variant in Remark.
- **Artwork Ideation:** `Visual: …` scene + callouts in quotes separated by ` | `.
- **CTA:** the caption's CTA.
- **Hashtags:** 5-6 tags, ending with `brand_hashtag`.
- **Artwork Link, Live Link, Engagement:** blank.
- **Status:** `Planned`.
- **Remark:** only items the team must check (unconfirmed URL, case-study facts, event participation, new product spotted, X variant).

Tone: follow `tone` and past calendars; write in `language`. Short, clear sentences. Never invent certifications, specs, figures, customer names, testimonials or results — a made-up claim in a published post can cause real legal and reputational harm to the brand.

## 8. Output

Per `output`:
- **`new-tab`:** add a tab named for the target month to the past-calendars sheet (needs a Google Sheets connector with write access), after the latest month. Header row bold and frozen, text wrapped, values written as raw text so dates aren't converted. Never edit or delete existing tabs.
- **`new-sheet`:** create a Google Sheet named `<brand_name> - Social Media Calendar - <month>` (e.g. upload an .xlsx through a Google Drive connector with conversion).
- **`xlsx`**, or whenever the chosen output isn't possible: build an .xlsx with one sheet for the month (header bold and frozen, text wrapped, sensible column widths; use an `xlsx` skill if listed) plus a UTF-8 .csv, and give both to the user. Say in one line why you fell back, if you did.

End with a short summary: link or file, number of posts, promotional vs non-promotional mix, special days used, new / renamed / removed products noticed on the website, and every Remark needing action.

## 9. Self-check before delivering

- Post count is right and every date is a working day in the target month, at `posting_time`.
- Every product mentioned exists on the website today, under its current name, with an opened URL.
- 3-4 awareness posts with no product names (scaled for other counts).
- No topic or product repeats from the last 3 months.
- All columns present in order; no empty Copy, Caption, CTA or Hashtags.
- No invented statistics, claims or customer names.
- Existing tabs and files untouched.

## Running it every month

If the user wants this automatic, set up a monthly scheduled task (e.g. on the 20th-25th) whose prompt includes the full settings (or points to the config file) and says "Use the monthly-social-media-calendar skill to plan next month." Since a scheduled run can't ask questions, the prompt must contain `brand_name` and `website` at minimum.
