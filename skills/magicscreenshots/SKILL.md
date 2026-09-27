---
name: magicscreenshots
description: Search a library of real App Store screenshots and preview videos from the top free and top-grossing apps in every category (updated daily), then generate, restyle or localize the user's own App Store screenshots. Use when the user is designing app screens, App Store screenshots, an App Store listing or product page, or an app preview video, or wants design inspiration from successful iOS apps.
---

# MagicScreenshots

MagicScreenshots is a library of real App Store listings: the screenshots and
preview videos of the top 100 free and top-grossing apps in every Apple
category, refreshed daily, with listing history. Search it for design
reference, then generate the user's own App Store screenshots in a chosen
style, or localize them.

The library shows **App Store listing screenshots** (the marketing images on
the product page, 1290×2796), not in-app UI captures.

## When to use

- The user is designing App Store screenshots, a product page, or an app
  preview video and wants to see what top apps do.
- The user wants inspiration for a category ("finance apps", "top-grossing
  games"), a look ("dark", "device frame", "big headline"), or a specific app.
- The user wants their own screenshots restyled like a reference app, or
  translated into other languages.

## Connect

Hosted MCP server (Streamable HTTP):

```
https://www.magicscreenshots.com/api/mcp
```

Search and read tools need no key. Generation, live App Store lookup, video
downloads and listing history send `Authorization: Bearer ms_live_...`.
Get a key with the `create_api_key` tool; the human opens its `claim_url`
to sign in.

No MCP client? Use the CLI (Node 18+):

```
npx magic-screenshots search --category finance --chart grossing
npx magic-screenshots app duolingo
npx magic-screenshots screens duolingo --out ./reference
```

Set `MAGICSCREENSHOTS_API_KEY` (or pass `--key`) for Pro and generation commands.

## Tools

Library (no key):

| Tool | Use |
|---|---|
| `list_directory_categories` | Apple categories with app counts (slugs like `finance`, `food-drink`). |
| `list_directory_tags` | Listing looks with counts: `headline`, `device-frame`, `lifestyle`, `dark`, `character`, `social-proof`, ... |
| `list_directory_apps` | Search. `query`, `category`, `tag`, `chart` (`free` or `grossing`, with `category`), `has_video`, `limit`, `offset`. Returns slugs. |
| `get_directory_app` | One app: developer, genre, chart ranks, looks, ordered screenshot URLs, video posters, related apps, ready-made `create_restyle` arguments. |
| `get_directory_screens` | The ordered screenshots as images a vision model can read. Optional 1-based `index`. |
| `get_app_videos` | Preview video posters. With a Pro key: 1-hour mp4 links (about 30 s each). |
| `get_app_history` | Past versions of a listing, newest first (Pro key). |

Generate (key):

| Tool | Use |
|---|---|
| `create_api_key` | Mint a key without an account. Returns `token` and `claim_url`. |
| `get_screenshots` | The user's app screenshots, by `query`, `app_url` or `app_id`. `live=true` skips the library and reads the current App Store listing. |
| `create_restyle` | Restyle the user's screenshots like a library app. Pass `source_app` and `inspiration_slug`. |
| `create_localize` | Translate screenshots into `target_locales` (`JPN`, `DEU`, `FRA`, `BRA`, ...). |
| `get_job_group` | Poll a job group: status, `overview_url`, final outputs. |
| `approve_restyle` | Approve the overview and render the final screenshots. |
| `list_results` | Recent restyle and localize jobs for this key. |

## Workflow: design inspiration

1. Pin down the brief: the user's category, audience and the feel they want.
   Map the category to a slug with `list_directory_categories` if unsure.
2. Search two or three ways:
   - `list_directory_apps` with `category` and `chart: "grossing"` (what earns)
     and `chart: "free"` (what gets downloaded).
   - `list_directory_apps` with a `tag` for a look (`dark`, `headline`,
     `device-frame`, `lifestyle`).
   - `list_directory_apps` with `query` for apps the user names.
3. Pick 3 to 6 apps. Call `get_directory_screens` for each and look at the
   images. Read every screenshot, in order; the first two matter most because
   they show in search results.
4. Summarise the patterns, citing apps by name:
   - Headline style: length, tone (benefit or feature), size, position.
   - Device frame: full-bleed UI, framed phone, floating cards, or none.
   - Colour: background, brand colour use, light or dark.
   - Order of benefits: what screen 1 sells, what comes next, where social
     proof or awards appear.
   - Anything unusual worth copying or avoiding.
5. Recommend a direction for the user's app, with 1 or 2 library apps as the
   reference (their slugs are the `inspiration_slug` for generation).

## Workflow: make my App Store screenshots

1. `create_api_key` if you have no key. Keep the `token`; show the human the
   `claim_url`.
2. `get_screenshots` for the user's app (App Store URL, app ID, or name).
   Confirm it is the right app before going on.
3. Choose the reference with the user (see the inspiration workflow), then
   `create_restyle` with `source_app` and `inspiration_slug`, for example
   `{"source_app": {"query": "My App"}, "inspiration_slug": "duolingo", "limit": 5}`.
4. If the response asks for payment or sign-in, stop and tell the human what
   it costs and where to go (`claim_url`). Do not pay on their behalf.
5. Poll `get_job_group` with the `group_id` every 10 to 20 seconds until
   `overview_url` appears. Show the overview to the human.
6. Only after the human approves, call `approve_restyle`. Poll `get_job_group`
   again until the final screenshot URLs are ready, then share them.

## Workflow: localize

1. `get_screenshots` for the source app (or use the restyled outputs).
2. Ask which markets matter. `create_localize` with `source_app` and
   `target_locales` (for example `["JPN", "DEU", "FRA"]`).
3. Same payment rule as above. Poll `get_job_group` until each locale's
   outputs are ready. Right-to-left languages (Arabic, Hebrew) mirror the layout.

## Workflow: preview video reference

1. `list_directory_apps` with `has_video: true` (add `category` to stay in
   the user's space).
2. `get_app_videos` for 2 or 3 apps. Everyone gets poster frames; a Pro key
   adds mp4 links that expire after an hour.
3. Describe what the videos do: the opening seconds, pacing, captions, how
   they show the UI, and how they end. Use that to outline the user's own
   30-second preview. Do not reuse the footage.

## Listing history (Pro)

`get_app_history` shows how an app's screenshots and videos changed over time.
Use it to show the user what top apps test and keep. Without a Pro key it
explains how to upgrade; pass that on rather than guessing.

## Rules

- Library screenshots and videos are third-party marketing assets that belong
  to their developers. Use them as reference only. Never present them as the
  user's work, and never ship them in the user's listing.
- Do not invent apps, ranks or looks. Only cite what the tools returned.
- Ask the human before anything that costs money: buying credits, upgrading,
  or approving a restyle. Never enter payment details yourself.
- Keep the API key out of chat transcripts and files the user will share.
- Generated screenshots should show the user's real app. Point out anything
  that misrepresents it, since App Review rejects misleading screenshots.

## Links

- Library: https://www.magicscreenshots.com/apps
- Top charts: https://www.magicscreenshots.com/top
- Agent setup: https://www.magicscreenshots.com/agents
- Pricing: https://www.magicscreenshots.com/pricing
