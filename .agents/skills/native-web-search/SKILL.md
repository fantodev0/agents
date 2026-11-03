---
name: native-web-search
description: "Trigger native web search. Use when you need quick internet research with concise summaries and full source URLs."
---

# Native Web Search

Use this skill to run a fast research model and get a concise summary with explicit full URLs. Cloud providers use native web search; LM Studio uses a local-model synthesis of fetched search results.

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
node search.mjs "LM Studio tool calling" --provider lmstudio --model qwen.qwen3.8-27b
```

Optional flags:

- `--provider openai-codex|anthropic|lmstudio`
- `--model <model-id>`
- `--timeout <ms>`
- `--json`

## Output expectations

For OpenAI Codex and Anthropic, the script uses each provider's native web-search tool. For LM Studio, local models cannot independently browse: the script searches DuckDuckGo's HTML results endpoint, then sends the result titles, snippets, and canonical URLs to the configured local model for synthesis.

The model is instructed to:
- provide a concise summary for the given purpose
- include full canonical URLs (`https://...`) for each key finding
- highlight disagreements between sources

## Notes

- No extra npm install is required.
- If module resolution fails, set `PI_AI_MODULE_PATH` to `@mariozechner/pi-ai`'s `dist/index.js` path.
- If OAuth helper resolution fails, set `PI_AI_OAUTH_MODULE_PATH` to `@mariozechner/pi-ai`'s `dist/oauth.js` path.
- For OAuth providers, the script can fall back to a still-valid cached `access` token from `~/.pi/agent/auth.json`.
- LM Studio is read from the `lmstudio` provider in `~/.pi/agent/models.json` (typically `baseUrl: "http://localhost:1234/v1"`). Its configured API key and headers are used for the local request.
- When called from Pi, `PI_PROVIDER` and `PI_MODEL` select the active LM Studio model automatically. Otherwise pass `--provider lmstudio` and optionally `--model`.
- LM Studio searches send the query to DuckDuckGo; the model synthesis stays local.
