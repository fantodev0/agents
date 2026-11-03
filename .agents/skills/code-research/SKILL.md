---
name: code-research
description: Perform evidence-based coding research before implementing any third-party APIs, SDKs, library and Frameworks. Use this skill for code research, implementation planning, integration debugging, version migration.
metadata:
  short-description: Research APIs and Libraries before implementation.
---

# Code Research

Research first. Code second.

## Core Policy

- Do not rely on memory alone for third-party API/Libraries/Frameworks.
- Use DeepWiki MCP **via mcporter** as a primary source when applicable.
- Cross-check with official docs and additional web source.

## MCP Usage Rule

When using any MCP for research, follow the `mcporter-mcp` skill: Always use `mcporter` (never direct MCP calls).

## Research Workflow

1. **Identify target dependencies and versions.**

- Extract package/libraries/framework version from project file (`package.json`, `*.csproj`, lockfiles, requirements, etc.) when available.

2. **Research vie DeepWiki MCP (through mcporter)**

```bash
npx -y mcporter list deepwiki
npx -y mcporter call deepwiki.read_wiki_structure repoName=owner/repo
npx -y mcporter call deepwiki.read_wiki_content repoName=owner/repo
npx -y mcporter call 'deepwiki.ask_question(repoName: "owner/repo", question: "How should i implement X in version Y?")'
```

3. **Cross-check with primary sources**

- Use `web_search`for targeted, version-aware queries
- Use `fetch_content` for official documentation, changelogs, and Github repositories
- Prefer official docs/repo content over secondary blog posts.

4. **Resolve conflicts explicitly**

- If sources disagree, prioritize :
  1. official docs/change logs for the exact versions
  2. Repository source code
  3. Recent, Credible Secondary Source.
- State uncertainty clearly when evidence is incomplete.

5. **Only then implement code**

- Base implementation on research findings.
- Mention critical version assumptions and keys findings before coding

## Mandatory Triggers.

Use this skill whenever:

- Integrating or configuring a third-party API, SDK, library or framework.
- Using unfamiliar library functions, options or behaviors.
- Handling dependency upgrades or breaking changes.
- Debugging issue likely caused by external dependency behaviors.
- Making architectural decisions based on framework/library capabilities.
