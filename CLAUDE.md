# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file Python CLI tool (`substack-archive`) that archives Substack newsletters as self-contained HTML files. It uses Substack's API to list posts and delegates HTML rendering to the `single-file-cli` npm package.

## Dependencies

- **Python 3.10+** (uses `datetime.UTC` and `X | Y` union type syntax)
- **`requests`** library (`pip install requests`)
- **`single-file-cli`** npm package (`npm install -g single-file-cli`) — required at runtime for HTML capture

No build system, tests, linting, or packaging configuration exists. The entire tool is one executable script.

## Running

```bash
./substack-archive <substack_url> [options]
```

Key flags: `-c <cookies.txt>` for paid content auth, `--incremental` for updates only, `--dry-run` to list without downloading, `-v` for verbose output.

## Architecture

The script (`substack-archive`, ~515 lines) is organized into these sections:

1. **Constants** (line ~43): API paths, retry/delay settings
2. **Helpers** (line ~59): URL parsing, slug generation, cookie-based session builder
3. **Manifest** (line ~115): JSON-based state tracking (`.archive-manifest.json`) for incremental archiving
4. **Substack API** (line ~137): Paginated post fetching from `/api/v1/archive`, metadata extraction
5. **Download engine** (line ~199): Wraps `single-file` CLI with 3-attempt retry, 120s timeout, file-size validation (>500 bytes)
6. **Index generator** (line ~244): Produces browsable `index.html` organized by year with paid/failed badges
7. **Main** (line ~323): Argument parsing, pre-flight checks, download loop with periodic manifest saves

### Data flow

`Substack API → post list → filter (incremental/limit) → single-file CLI per post → manifest + index.html`

### File output structure

```
<output_dir>/
  .archive-manifest.json      # download state for incremental updates
  index.html                  # browsable archive index
  YYYY-MM-DD_slug.html        # archived post (self-contained)
  YYYY-MM-DD_slug.json        # optional per-post metadata (--json-metadata)
```
