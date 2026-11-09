---
name: native-web-search
description: "Trigger web search. Use when you need quick internet research with concise summaries and full source URLs."
---

# Native Web Search

Use this skill to run a **fast model with web search** and get a concise research summary with explicit full URLs. Cloud providers use native web search; LM Studio uses a local-model synthesis of fetched search results.

## Script

- `search.mjs`

## Usage

Run from this skill directory:

```bash
node search.mjs "<what to search>" --purpose "<why you need this>"
```

Examples:

```bash
node search.mjs "latest python release" --purpose "update dependency notes"
node search.mjs "vite 7 breaking changes" --purpose "prepare migration checklist"
node search.mjs "vite 7 breaking changes" --provider solid --json
node search.mjs "LM Studio tool calling" --provider lmstudio --model qwen.qwen3.8-27b
```

Optional flags:

- `--provider openai-codex|anthropic|solid|lmstudio`
- `--model <model-id>`
- `--timeout <ms>`
- `--max-rounds <n>` (Solid only, default `6`)
- `--json`
- `--dry-run` (print the resolved provider, model, base URL, and credential status, then exit)

## Providers

| Provider       | Default model                          | Web search                                 |
| -------------- | -------------------------------------- | ------------------------------------------ |
| `openai-codex` | fastest available Codex model          | server-side `web_search` tool              |
| `anthropic`    | fastest available Claude Haiku model   | server-side `web_search_20250305` tool     |
| `solid`        | `deepseek/deepseek-v4.1-flash`         | local `web_search` / `web_fetch` tool loop |
| `lmstudio`     | first model in the `lmstudio` provider | local DuckDuckGo search + local synthesis  |

For LM Studio, local models cannot independently browse: the script searches DuckDuckGo's HTML results endpoint, then sends the result titles, snippets, and canonical URLs to the configured local model for synthesis.

### Solid provider

"Solib"/"Solid" is the `solid` provider from Pi's `models.json`
(`https://accounts.solid.xyz/accounts/v1/ai/v1`, OpenAI-compatible
`chat/completions`). Its default search model is `deepseek/deepseek-v4.1-flash`;
pass `--model` to override it for a single run.

The Solid API has **no server-side web search** (it rejects `type: "web_search"`
tools and silently ignores `web_search_options` / `plugins`). The script therefore
runs a tool loop: the model calls `web_search` (DuckDuckGo HTML results) and
`web_fetch` (page text), then writes the summary itself.

Provider selection order:

1. `--provider`
2. a provider in `models.json` that declares `--model`
3. `PI_PROVIDER` (the active Pi session, e.g. `PI_PROVIDER=solid` or `PI_PROVIDER=lmstudio`)
4. `settings.json` `defaultProvider`
5. available credentials (`openai-codex`, then `anthropic`)

Provider-specific resolution:

- `solid` uses `providers.solid` in `models.json` (`baseUrl`, `apiKey`).
- `lmstudio` uses `providers.lmstudio` in `models.json` (`baseUrl`, `apiKey`,
  `headers`), falling back to `PI_MODEL` / `settings.json defaultModel` when no
  `--model` is passed.

Credentials for Solid resolve in this order:

1. `providers.solid.apiKey` in `$PI_CODING_AGENT_DIR/models.json`
   (default `~/.pi/agent/models.json`), then `./.pi/models.json`
2. `SOLID_API_KEY`
3. a `solid` entry in `auth.json`

Check the wiring without making a request:

```bash
node search.mjs "test" --provider solid --dry-run
```

## Output expectations

The script instructs the model to:

- search the internet for the requested topic
- provide a concise summary for the given purpose
- include full canonical URLs (`https://...`) for each key finding
- highlight disagreements between sources

## Notes

- No extra npm install is required.
- If module resolution fails, set `PI_AI_MODULE_PATH` to `@mariozechner/pi-ai`'s `dist/index.js` path.
- If OAuth helper resolution fails, set `PI_AI_OAUTH_MODULE_PATH` to `@mariozechner/pi-ai`'s `dist/oauth.js` path.
- For OAuth providers, the script can fall back to a still-valid cached `access` token from `~/.pi/agent/auth.json`.
- The Solid path does not use `pi-ai` at all.
- LM Studio is read from the `lmstudio` provider in `~/.pi/agent/models.json` (typically `baseUrl: "http://localhost:1234/v1"`). Its configured API key and headers are used for the local request.
- When called from Pi, `PI_PROVIDER` and `PI_MODEL` select the active LM Studio model automatically. Otherwise pass `--provider lmstudio` and optionally `--model`.
- LM Studio searches send the query to DuckDuckGo; the model synthesis stays local.
