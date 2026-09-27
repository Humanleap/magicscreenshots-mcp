# MagicScreenshots for agents

Give your AI agent a library of real App Store screenshots and preview videos
from thousands of top apps, updated daily, then let it generate and localize
your own App Store screenshots.

- **Library**: the top 100 free and top-grossing apps in every Apple category,
  with screenshots, preview videos and listing history.
- **Search** by category, today's chart, look (dark, headline, device frame,
  lifestyle, ...) or app name.
- **Generate**: restyle your screenshots like any app in the library, or
  translate them into 40+ languages at App Store resolution.

Setup guides for every agent: https://www.magicscreenshots.com/agents

MCP endpoint (Streamable HTTP): `https://www.magicscreenshots.com/api/mcp`

## Install

Searching needs no key. Generation, video downloads and listing history need
an API key: ask your agent to call `create_api_key`, or create one in
Settings > API on magicscreenshots.com, then set `MAGICSCREENSHOTS_API_KEY`.

### Any agent with skills (Claude Code, Codex, Cursor, Cline, Windsurf, ...)

```sh
npx skills add Humanleap/magicscreenshots-mcp
```

This installs the skill only. Add the MCP server too (below) so the agent can
call the tools.

### Claude Code

Plugin (skill + MCP server):

```
/plugin marketplace add Humanleap/magicscreenshots-mcp
/plugin install magicscreenshots@magicscreenshots
```

Or just the MCP server:

```sh
claude mcp add --transport http magicscreenshots https://www.magicscreenshots.com/api/mcp \
  --header "Authorization: Bearer $MAGICSCREENSHOTS_API_KEY"
```

The plugin sends `Authorization: Bearer ${MAGICSCREENSHOTS_API_KEY}`. Set the
variable before starting Claude Code. With no key set, search still works, but
`get_app_videos`, `get_app_history` and generation return an auth error. For
fully keyless use, delete the `headers` block from the plugin's `.mcp.json`.

### Cursor

Install the plugin from the Cursor Marketplace, or add this to
`.cursor/mcp.json` (project) or `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "magicscreenshots": {
      "url": "https://www.magicscreenshots.com/api/mcp",
      "headers": { "Authorization": "Bearer ${env:MAGICSCREENSHOTS_API_KEY}" }
    }
  }
}
```

Without `MAGICSCREENSHOTS_API_KEY` set, leave out `headers` (same reason as
Claude Code above).

### Codex

```sh
codex mcp add magicscreenshots --url https://www.magicscreenshots.com/api/mcp
```

For generation, set the key in `config.toml`:

```toml
[mcp_servers.magicscreenshots]
url = "https://www.magicscreenshots.com/api/mcp"
bearer_token_env_var = "MAGICSCREENSHOTS_API_KEY"
```

### Gemini CLI

Extension (skill + MCP server, search tools):

```sh
gemini extensions install https://github.com/Humanleap/magicscreenshots-mcp
```

With a key:

```sh
gemini mcp add --transport http --header "Authorization: Bearer $MAGICSCREENSHOTS_API_KEY" \
  magicscreenshots https://www.magicscreenshots.com/api/mcp
```

### Any other MCP client

```json
{
  "mcpServers": {
    "magicscreenshots": {
      "type": "http",
      "url": "https://www.magicscreenshots.com/api/mcp",
      "headers": { "Authorization": "Bearer ms_live_..." }
    }
  }
}
```

Some clients call the URL field `serverUrl` (Windsurf) or `httpUrl` (Gemini).
Leave out `headers` to use search only.

### Command line

```sh
npx magic-screenshots search --category finance --chart grossing
npx magic-screenshots app duolingo
npx magic-screenshots screens duolingo --out ./reference
npx magic-screenshots videos instagram
```

## Tools

| Tool | Key | What it does |
|---|---|---|
| `list_directory_apps` | no | Search the library by text, category, look, chart, or apps with preview videos |
| `list_directory_categories` | no | Apple categories with app counts |
| `list_directory_tags` | no | Listing looks (headline, device-frame, dark, ...) with app counts |
| `get_directory_app` | no | One app: developer, chart ranks, looks, screenshots, video posters |
| `get_directory_screens` | no | Ordered screenshots as images your agent can look at |
| `get_app_videos` | Pro for files | Preview video posters; mp4 downloads with Pro |
| `get_app_history` | Pro | Past versions of a listing |
| `create_api_key` | no | Mint a key; you claim it in the browser |
| `get_screenshots` | for live lookup | Your app's current App Store screenshots |
| `create_restyle` | yes | Restyle your screenshots like a library app |
| `create_localize` | yes | Translate your screenshots into other languages |
| `get_job_group`, `approve_restyle`, `list_results` | yes | Follow and approve generation jobs |

The skill in [`skills/magicscreenshots/SKILL.md`](skills/magicscreenshots/SKILL.md)
tells the agent how to run an inspiration review, make App Store screenshots,
localize, and study preview videos.

## Pricing

- **Free**: search the library and view screenshots.
- **Library Pro**: $19, adds preview video downloads and listing history.
- **Team**: $49 per seat.
- **Image credits**: generation (restyle and localize) is paid per image.

Current prices: https://www.magicscreenshots.com/pricing

## Legal

Screenshots and preview videos in the library belong to their developers.
MagicScreenshots shows them for reference and research. Don't reuse them as
your own App Store assets.

## Maintaining

The web copy at `public/.well-known/agent-skills/magicscreenshots/SKILL.md` in
the MagicScreenshots app repo is the source. Copy it here unchanged:

```sh
cp public/.well-known/agent-skills/magicscreenshots/SKILL.md kit/skills/magicscreenshots/SKILL.md
```

The app repo's `scripts/agent-cli.test.mjs` fails if the two copies differ or
if the digest in `agent-skills/index.json` is stale.

## License

MIT © Humanleap. See [LICENSE](LICENSE).
