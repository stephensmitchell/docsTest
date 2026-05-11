# Blog Post Frontmatter Cheatsheet

A reference for everything The Tool Store recognizes at the top of a `.md`
file. Drop a `TEMPLATE.md` next to this file for a copy-paste starting point.

## How the frontmatter is parsed

A post is just a markdown file. The optional **frontmatter block** at the very
top is a YAML-ish header delimited by two `---` lines:

```markdown
---
title: My Post Title
publishedAt: 2026-05-11
tags: [foo, bar]
---

Markdown body starts here.
```

Rules:

- The opening `---` must be the **very first line** of the file. If it isn't,
  the whole file is treated as the body and frontmatter defaults apply.
- Closing `---` must appear on its own line.
- Each field is `key: value` on one line. No nested objects.
- Arrays use bracket form: `[item1, item2, item3]`. Quoted items are unquoted
  during parsing.
- `true` / `false` are interpreted as booleans.
- Plain numeric values (e.g. `42`) become numbers.
- Anything else becomes a string (with surrounding quotes stripped).

## Recognized fields

| Field | Type | Required | Fallback when omitted |
| ----- | ---- | -------- | --------------------- |
| `title` | string | no | First `# heading` in the body, or the filename slug |
| `author` | string | no | The repo owner, gist owner, or "The Tool Store" |
| `publishedAt` | ISO date / `YYYY-MM-DD` | no | The file's creation timestamp |
| `date` | (alias for `publishedAt`) | no | — |
| `updatedAt` | ISO date | no | The file's modification timestamp |
| `categories` | string array | no | `[]` |
| `category` | (alias for `categories`) | no | — |
| `tags` | string array | no | `[]` |
| `keywords` | string array | no | `[]` |
| `excerpt` | string | no | First ~220 chars of the body, code blocks + links stripped |
| `description` | (alias for `excerpt`) | no | — |
| `coverImage` | URL string | no | none |
| `featured` | boolean | no | `false` |
| `status` | `published` \| `draft` | no | `published` |
| `readingTimeMinutes` | number | no | Computed from word count (~220 wpm) |

## Field semantics

### `title`

Shown as the heading in the reader and on every card / list row.

Fallback chain:
1. Frontmatter `title`.
2. The first `# heading` (or `##`, `###`, …) found in the body.
3. The filename without `.md` (e.g. `my-post.md` → `my-post`).

### `author`

Shown next to the avatar in the reader and below the title in list/card views.
The avatar itself defaults to the GitHub avatar of the repo / gist owner when
available; you can override with `authorAvatar:` in frontmatter (URL).

### `publishedAt` / `date`

Used for the default sort (`Newest first`) and shown in the reader. Either
field works. Accepts ISO 8601 (`2026-05-11T13:24:00Z`) or `YYYY-MM-DD`.

If both `publishedAt` and `date` are set, `publishedAt` wins.

### `updatedAt`

Used by the `Recently updated` sort mode. Auto-set to the file's mtime if
omitted.

### `categories`

Each category is a **slug** (lowercase, hyphenated). The Tool Store
auto-creates a `BlogCategory` for each unique slug referenced, with a
title-cased display name. Admins can edit name, description, and color in
`CMS → Blog → Blog Categories`.

```yaml
categories: [news, getting-started, engineering]
```

### `tags`

Free-form lowercase strings. Show up in the filter sidebar's Tags section
with per-tag post counts. Anything goes — `tags: [release, beta, v0.1]`.

### `keywords`

Not displayed in the UI; only used by the sidebar's full-text Keyword search.
Add terms you want a reader to find the post by even if those words aren't
in the title or body.

### `excerpt` / `description`

The one- or two-sentence summary shown in the post list (list and card
views) and right under the title in the reader. If omitted, the system
generates one from the body — but a hand-written excerpt is almost always
better.

### `coverImage`

Absolute URL to a hero image shown above the post in the reader and on
medium / large cards in the list. Use a CDN-hosted image; local relative
paths won't resolve when the post is loaded from a remote source.

### `featured`

When `true`, the post:

- gets a `Featured` badge in the list,
- shows up in the `Featured only` filter,
- otherwise sorts normally.

Use sparingly — featured loses meaning if every post is featured.

### `status`

- `published` (default): visible to everyone.
- `draft`: visible only to admins. Useful for staging a post in the repo
  before it goes live.

### `readingTimeMinutes`

Optional override. By default the Tool Store estimates from the rendered
word count at ~220 words per minute. Set this if you want to force a
specific number (e.g. for a tutorial that includes interactive steps).

## Required body content

There is **no** mandatory body field — an empty body works. Recommended
minimum:

1. Lead with a `# Heading` that matches `title` (or omit `title` from
   frontmatter and let the heading drive the title).
2. One paragraph hook.
3. The actual content in `##` sections.

## Supported markdown features

The Tool Store renders posts via the `marked` library with GitHub-flavored
markdown enabled:

- Headings `#` through `######`
- **Bold**, *italic*, ***bold italic***, ~~strikethrough~~, `inline code`
- [Links](https://example.com) — open in the user's default browser
- Images via `![alt](url)`
- Fenced code blocks with language hints (\`\`\`ts, \`\`\`python, etc.)
- Bullet and numbered lists, including nested
- Task lists (`- [ ]`, `- [x]`)
- Tables with column alignment
- Blockquotes
- Horizontal rules (`---`)

## How The Tool Store ingests this file

1. The user adds this repo as a **Blog GitHub Repo Source** in
   `CMS → Blog → Blog GitHub Repo Sources` (owner + repo). Public repos do
   **not** require a token.
2. On every Blog-tab visit the app calls
   `GET https://api.github.com/repos/{owner}/{repo}/contents/{path}` once
   to list every file.
3. For each `.md` file, it fetches the raw body from
   `raw.githubusercontent.com` (CDN, never rate-limited, no auth needed for
   public repos).
4. Frontmatter is parsed, body is kept as the post content, and the result
   is written into the user's local CMS store under a stable ID
   (`gh-{owner}-{repo}-{path}`).
5. The user's `enabled` flag is preserved across syncs — i.e. if they
   disable a post once, it stays disabled even when the file refreshes.

## How it works for a Gist

Identical, but the source URL is `gists/{gistId}`. Stable post IDs are
`gist-{gistId}-{filename-slug}`. Auto-detected author is the gist owner
from the API response.

## Common gotchas

- **Indented `---`**: the opening / closing fence must start at column 0,
  no leading spaces.
- **Multi-line strings**: not supported. Keep each frontmatter value on a
  single line. For long excerpts, write them as one long line.
- **Quoted keys**: not supported. Keys are plain identifiers.
- **Empty arrays**: write `tags: []` — leaving the right side blank parses
  as an empty string.
- **Drafts on public sources**: `status: draft` files will still be fetched
  from the public repo, but only admins of The Tool Store install will see
  them in the Blog tab. The body is still readable by anyone with the repo
  URL — *do not* put secrets in a draft post.

## Minimum viable post

```markdown
---
title: My First Post
publishedAt: 2026-05-11
---

# My First Post

A one-paragraph body. That's the whole minimum.
```

Everything else falls back to a sensible default.
