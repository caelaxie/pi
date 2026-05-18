# Codebase Wiki Instructions

## Purpose

This directory contains beginner-friendly tutorials for learning Pi internals.
Write for readers who know little about agent development or this repository.

## Format

- Prefer self-contained HTML files over Markdown for tutorial pages.
- Keep each page readable through `file://` with no build step, server, or external assets.
- Use inline CSS and inline SVG when diagrams help explain a flow.
- Do not include dependency graphs in the website.
- Keep code blocks high contrast. If styling inline `code`, also define `pre code` so code block contents remain readable.

## Content Style

- Teach concepts through concrete repo files and links.
- Explain the mental model first, then point to implementation details.
- Use concise technical prose; avoid marketing copy.
- Prefer lifecycle diagrams, sequence diagrams, and small annotated examples over long prose.
- Keep pages beginner-oriented, but do not hide important implementation boundaries.

## Source Grounding

- Before updating a tutorial, inspect the current source files it references.
- Verify all relative links from `.codebase-wiki/` still resolve.
- If behavior has changed in the repo, update the tutorial to match the code rather than preserving stale explanations.

## Verification

After editing wiki HTML, run a lightweight static check:

```bash
python3 - <<'PY'
from html.parser import HTMLParser
from pathlib import Path

class P(HTMLParser):
    pass

for path in sorted(Path(".codebase-wiki").glob("*.html")):
    parser = P()
    parser.feed(path.read_text())
    parser.close()
    print(f"parsed {path}")
PY
```

Also check local links when adding or changing anchors:

```bash
python3 - <<'PY'
from html.parser import HTMLParser
from pathlib import Path

class Links(HTMLParser):
    def __init__(self):
        super().__init__()
        self.hrefs = []

    def handle_starttag(self, tag, attrs):
        if tag == "a":
            href = dict(attrs).get("href")
            if href:
                self.hrefs.append(href)

root = Path(".").resolve()
errors = []
for path in sorted(Path(".codebase-wiki").glob("*.html")):
    parser = Links()
    parser.feed(path.read_text())
    for href in parser.hrefs:
        if href.startswith(("http://", "https://", "#", "mailto:")):
            continue
        target = (path.parent / href).resolve()
        if not str(target).startswith(str(root)) or not target.exists():
            errors.append(f"{path} -> {href}")

if errors:
    print("\n".join(errors))
    raise SystemExit(1)

print("all local links resolve")
PY
```
