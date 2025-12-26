# pi-agent-transcripts

Convert Claude Code and [Pi](https://github.com/badlogic/pi-mono) agent session files (JSON or JSONL) to clean, mobile-friendly HTML pages with pagination.

Fork of [simonw/claude-code-transcripts](https://github.com/simonw/claude-code-transcripts) with added Pi agent support.

## Installation

Install this tool using `uv`:
```bash
uv tool install pi-agent-transcripts
```
Or run it without installing:
```bash
uvx pi-agent-transcripts --help
```

## Usage

This tool converts Claude Code and Pi agent session files into browseable multi-page HTML transcripts.

There are four commands available:

- `local` (default) - select from local Claude Code sessions stored in `~/.claude/projects`
- `pi` - select from local Pi agent sessions stored in `~/.pi/agent/sessions`
- `web` - select from web sessions via the Claude API
- `json` - convert a specific JSON or JSONL session file

### Pi sessions

Pi agent sessions are stored as JSONL files in `~/.pi/agent/sessions`. Use the `pi` command to select from recent sessions:

```bash
pi-agent-transcripts pi
```

This shows an interactive picker to select a Pi session, generates HTML, and opens it in your default browser.

Use `--limit` to control how many sessions are shown (default: 10):

```bash
pi-agent-transcripts pi --limit 20
```

### Claude Code local sessions

Local Claude Code sessions are stored as JSONL files in `~/.claude/projects`. Run with no arguments to select from recent sessions:

```bash
pi-agent-transcripts
# or explicitly:
pi-agent-transcripts local
```

### Output options

All commands support these options:

- `-o, --output DIRECTORY` - output directory (default: writes to temp dir and opens browser)
- `-a, --output-auto` - auto-name output subdirectory based on session ID or filename
- `--repo OWNER/NAME` - GitHub repo for commit links (auto-detected from git push output if not specified)
- `--open` - open the generated `index.html` in your default browser (default if no `-o` specified)
- `--gist` - upload the generated HTML files to a GitHub Gist and output a preview URL
- `--json` - include the original session file in the output directory

The generated output includes:
- `index.html` - an index page with a timeline of prompts and commits
- `page-001.html`, `page-002.html`, etc. - paginated transcript pages

### Web sessions

Import sessions directly from the Claude API:

```bash
# Interactive session picker
pi-agent-transcripts web

# Import a specific session by ID
pi-agent-transcripts web SESSION_ID

# Import and publish to gist
pi-agent-transcripts web SESSION_ID --gist
```

On macOS, API credentials are automatically retrieved from your keychain (requires being logged into Claude Code). On other platforms, provide `--token` and `--org-uuid` manually.

### JSON/JSONL files

Convert a specific session file directly:

```bash
pi-agent-transcripts json session.json -o output-directory/
pi-agent-transcripts json session.jsonl --open
```

The tool auto-detects the format (Claude Code vs Pi) based on the file contents.

### Auto-naming output directories

Use `-a/--output-auto` to automatically create a subdirectory named after the session:

```bash
# Creates ./session_ABC123/ subdirectory
pi-agent-transcripts web SESSION_ABC123 -a

# Creates ./transcripts/session_ABC123/ subdirectory
pi-agent-transcripts web SESSION_ABC123 -o ./transcripts -a
```

### Publishing to GitHub Gist

Use the `--gist` option to automatically upload your transcript to a GitHub Gist and get a shareable preview URL:

```bash
pi-agent-transcripts --gist
pi-agent-transcripts pi --gist
pi-agent-transcripts web --gist
pi-agent-transcripts json session.json --gist
```

This will output something like:
```
Gist: https://gist.github.com/username/abc123def456
Preview: https://gistpreview.github.io/?abc123def456/index.html
Files: /var/folders/.../session-id
```

The preview URL uses [gistpreview.github.io](https://gistpreview.github.io/) to render your HTML gist. The tool automatically injects JavaScript to fix relative links when served through gistpreview.

Combine with `-o` to keep a local copy:

```bash
pi-agent-transcripts json session.json -o ./my-transcript --gist
```

**Requirements:** The `--gist` option requires the [GitHub CLI](https://cli.github.com/) (`gh`) to be installed and authenticated (`gh auth login`).

### Including the source file

Use the `--json` option to include the original session file in the output directory:

```bash
pi-agent-transcripts json session.json -o ./my-transcript --json
```

This will output:
```
JSON: ./my-transcript/session_ABC.json (245.3 KB)
```

This is useful for archiving the source data alongside the HTML output.

## Development

To contribute to this tool, first checkout the code. You can run the tests using `uv run`:
```bash
cd pi-agent-transcripts
uv run pytest
```
And run your local development copy of the tool like this:
```bash
uv run pi-agent-transcripts --help
```
