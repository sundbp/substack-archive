# substack-archive

A command-line tool for archiving Substack newsletters as self-contained HTML files. Supports both public and paid/private subscriptions, incremental updates, and generates a browsable index of your archive.

## Requirements

- Python 3.10+
- [`requests`](https://pypi.org/project/requests/) — `pip install requests`
- [`single-file-cli`](https://github.com/gildas-lormeau/single-file-cli) — `npm install -g single-file-cli`

## Usage

```bash
# Archive a public newsletter
./substack-archive https://example.substack.com

# Archive a paid newsletter using browser cookies
./substack-archive https://example.substack.com -c ~/cookies.txt

# Incrementally update an existing archive
./substack-archive https://example.substack.com -c ~/cookies.txt --incremental

# Preview what would be downloaded
./substack-archive https://example.substack.com --dry-run
```

## Options

| Flag | Description |
|------|-------------|
| `-o, --output` | Output directory (default: current directory) |
| `-c, --cookies-file` | Path to Netscape-format cookies.txt for authentication |
| `--incremental` | Only download new/updated posts |
| `--redownload-failed` | Retry previously failed downloads |
| `--limit N` | Max posts to download (0 = unlimited) |
| `--delay SECONDS` | Delay between downloads (default: 1.5s) |
| `--no-index` | Skip generating the HTML index page |
| `--dry-run` | List posts without downloading |
| `--json-metadata` | Save per-post JSON metadata alongside HTML |
| `-v, --verbose` | Verbose output |

## Authentication

For paid or private newsletters, export your Substack session cookies as a Netscape-format `cookies.txt` file using a browser extension such as "Get cookies.txt LOCALLY" (Chrome/Firefox), then pass it with `-c`.

## Output

The archive directory contains:

- `YYYY-MM-DD_slug.html` — each post as a self-contained HTML file
- `index.html` — browsable archive index organized by year
- `.archive-manifest.json` — download state used for incremental updates
