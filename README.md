# Tribute

A Claude Code skill to discover and fund the open source projects your code depends on.

## Why Tribute?

Your project depends on open source software maintained by volunteers and small teams. These maintainers often struggle to fund their work, yet their code powers your applications.

**The problem:** Finding funding links for your dependencies is tedious. Package registries don't surface this information well, and manually checking each project's GitHub is time-consuming.

**Tribute solves this** by automatically:
- Reading your dependency files
- Researching funding links from FUNDING.yml files and web searches
- Verifying every link actually works before showing it to you

## Install

Copy the skill file to your Claude Code commands directory:

```bash
curl -o ~/.claude/commands/tribute.md \
  https://raw.githubusercontent.com/jshchnz/tribute/main/tribute.md
```

Verify it worked:
```bash
ls ~/.claude/commands/tribute.md
```

## Usage

In Claude Code, navigate to any project and type:

```
/tribute
```

Claude will analyze your dependencies and return a funding report.

### Example Session

```
You: /tribute

Claude: I'll analyze this project's dependencies and find verified funding links.

[Reads package.json, finds 15 dependencies]
[Checks FUNDING.yml for express... found Open Collective]
[Checks FUNDING.yml for lodash... found Open Collective]
[Searches for "chalk" funding... found GitHub Sponsors]
[Verifying links...]

## Dependency Funding Report for my-app

### Summary
- Analyzed: 15 direct dependencies
- Verified funding found: 6 packages

### Verified Funding Links

| Package | Funding | How Verified |
|---------|---------|--------------|
| express | [Open Collective](https://opencollective.com/express) | FUNDING.yml |
| lodash | [Open Collective](https://opencollective.com/lodash) | FUNDING.yml |
| chalk | [GitHub Sponsors](https://github.com/sponsors/chalk) | FUNDING.yml |
| debug | [GitHub Sponsors](https://github.com/sponsors/debug-js) | Web search |
| dotenv | [GitHub Sponsors](https://github.com/sponsors/motdotla) | FUNDING.yml |
| commander | [GitHub Sponsors](https://github.com/sponsors/shadowspawn) | FUNDING.yml |

### No Verified Funding Found

These packages don't have discoverable funding pages:
- react (maintained by Meta)
- typescript (maintained by Microsoft)
- @types/* (DefinitelyTyped community project)

### Quick Actions

Support these projects:
1. https://opencollective.com/express
2. https://opencollective.com/lodash
3. https://github.com/sponsors/chalk
```

## How It Works

Tribute uses a multi-step verification process to ensure every funding link is accurate:

### Step 1: Read Dependencies

Tribute reads your project's dependency files:

**Node.js (package.json):**
```json
{
  "dependencies": {
    "express": "^4.18.0",
    "lodash": "^4.17.21"
  }
}
```

**Python (requirements.txt):**
```
requests==2.31.0
flask>=2.0.0
```

**Rust (Cargo.toml):**
```toml
[dependencies]
serde = "1.0"
tokio = { version = "1", features = ["full"] }
```

### Step 2: Check FUNDING.yml

For each dependency, Tribute checks if the GitHub repo has a `.github/FUNDING.yml` file:

```yaml
# Example FUNDING.yml from expressjs/express
open_collective: express
github: [dougwilson]
```

This is parsed to extract funding links:
- `open_collective: express` → https://opencollective.com/express
- `github: dougwilson` → https://github.com/sponsors/dougwilson

### Step 3: Web Search Fallback

If no FUNDING.yml exists, Tribute searches for funding pages:

```
"{package name}" github sponsors OR open collective OR funding
```

### Step 4: Verify Links

**Every link is verified before being shown.** This is crucial because:
- GitHub Sponsors pages only exist if the user enrolled
- Open Collective pages only exist if the project created one
- Training data about funding links may be outdated

Tribute fetches each URL and checks for valid responses. Broken links are excluded.

## Supported Ecosystems

| Ecosystem | File | Example |
|-----------|------|---------|
| Node.js/npm | `package.json` | `"express": "^4.18.0"` |
| Python | `requirements.txt` | `requests==2.31.0` |
| Python | `pyproject.toml` | `dependencies = ["flask>=2.0"]` |
| Rust | `Cargo.toml` | `serde = "1.0"` |
| Go | `go.mod` | `require github.com/gin-gonic/gin v1.9.0` |
| Ruby | `Gemfile` | `gem 'rails', '~> 7.0'` |

## Why Verified Links?

Many tools guess funding URLs based on package names (e.g., assuming `opencollective.com/{package}` exists). This leads to broken links.

**Tribute is different:** Every link is fetched and verified before being presented. If a link doesn't work, it's not shown.

| Approach | Problem |
|----------|---------|
| Guessing `opencollective.com/{name}` | Many packages don't have Open Collective pages |
| Guessing `github.com/sponsors/{user}` | Not all maintainers have enrolled in Sponsors |
| Using training data | Funding pages change over time |
| **Tribute's approach** | Verify every link exists before showing it |

## License

MIT
