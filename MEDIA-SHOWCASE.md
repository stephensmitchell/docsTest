---
title: Media Showcase
coverImage: https://raw.githubusercontent.com/stephensmitchell/docsTest/main/clipboard-image-1.jpg
tags: [media, reference, examples]
categories: [reference]
excerpt: Every media pattern The Tool Store's blog renderer supports — images, videos, iframes, audio, captions, and gallery layouts.
---

# Media Showcase

A reference post demonstrating every media pattern The Tool Store
renders. Use any of these directly in your own posts.

## 1. Cover image (frontmatter)

The `coverImage` field at the top of this file renders the hero image
above the title in the reader and on medium / large cards in the post
list. Use an absolute URL — relative paths only resolve for the local
manifest.

The cover for this post is `clipboard-image-1.jpg`, bundled in this
same GitHub repo and served via the `raw.githubusercontent.com` CDN.

## 2. Images bundled in this source

Markdown image syntax with the raw GitHub URL of a file checked in
alongside this post. The image renders just like any other markdown
image — no special config needed.

![clipboard-image-2.jpg](https://raw.githubusercontent.com/stephensmitchell/docsTest/main/clipboard-image-2.jpg)

![clipboard-image-3.jpg](https://raw.githubusercontent.com/stephensmitchell/docsTest/main/clipboard-image-3.jpg)

![clipboard-image-4.jpg](https://raw.githubusercontent.com/stephensmitchell/docsTest/main/clipboard-image-4.jpg)

Raw-URL pattern for repo-hosted images:
`https://raw.githubusercontent.com/{owner}/{repo}/{branch}/{path-to-file}`

## 3. Markdown images from an external CDN

Standard `![alt](url)` syntax also works with any third-party CDN. The
blog's CSS (`.blog-prose img`) keeps images responsive with rounded
corners and vertical spacing.

![A wide placeholder photo](https://picsum.photos/seed/showcase-1/1200/600)

A caption can go in the paragraph below the image:

> *Figure 1.* Placeholder photo at 1200×600. Source: picsum.photos.

## 3. HTML `<img>` with explicit sizing

Use raw HTML when you need attributes the markdown syntax doesn't expose
(width, height, loading, decoding hints).

<img
  src="https://picsum.photos/seed/showcase-2/800/450"
  alt="Centered image with explicit width"
  width="800"
  height="450"
  loading="lazy"
  style="display:block; margin: 1em auto;"
/>

## 4. Inline image (smaller, in-flow)

Logos, icons, and other in-flow visuals work with regular `<img>` too:

<img src="https://picsum.photos/seed/inline-icon/64/64" alt="Tiny icon" width="32" height="32" style="display:inline; vertical-align:middle; margin-right:6px;" />
This sentence has an inline image to its left.

## 5. Direct video file (HTML5 `<video>`)

For self-hosted MP4 / WebM video, embed a `<video>` tag with controls:

<video
  src="https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4"
  controls
  preload="metadata"
  width="100%"
  style="max-width: 720px; display: block; margin: 1em auto; border-radius: 8px;"
></video>

Tips:

- `preload="metadata"` loads enough to show the player and duration
  without downloading the whole file.
- `controls` exposes the native player UI; remove if you want a
  background or hero video and add `autoplay muted loop playsinline`.
- For multiple formats (browser fallback), use `<source>` children:

```html
<video controls width="100%" style="max-width: 720px;">
  <source src="https://example.com/clip.webm" type="video/webm" />
  <source src="https://example.com/clip.mp4" type="video/mp4" />
  Your browser does not support HTML5 video.
</video>
```

## 6. YouTube embed (`<iframe>`)

YouTube's standard responsive embed. Wrap in an aspect-ratio container
so it sizes nicely:

<div style="position: relative; width: 100%; max-width: 720px; aspect-ratio: 16/9; margin: 1em auto;">
  <iframe
    src="https://www.youtube-nocookie.com/embed/M7lc1UVf-VE"
    title="Sample YouTube embed"
    style="position: absolute; inset: 0; width: 100%; height: 100%; border: 0; border-radius: 8px;"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen
  ></iframe>
</div>

> Using `youtube-nocookie.com` instead of `youtube.com` is the
> privacy-enhanced mode — fewer cookies, no recommended-videos overlay
> until the user interacts.

> **Embeddability gotcha — YouTube error 153 / 150 / 101.** Some
> YouTube videos disable embedding (uploader setting, content-ID
> conflict, or region restriction). If the player shows
> "Video player configuration error" or "Video unavailable":
>
> 1. Open `https://www.youtube.com/watch?v={VIDEO_ID}` in a browser —
>    if the video plays there but the embed errors, embedding is
>    disabled by the uploader.
> 2. Try `https://www.youtube.com/embed/{VIDEO_ID}` directly. If
>    that errors with the same code, no client can embed it.
> 3. Pick a different video, or host the file yourself and use the
>    HTML5 `<video>` pattern in section 5 instead.
>
> The example above uses `M7lc1UVf-VE`, which is YouTube's own canonical
> "embeddable" sample referenced in their IFrame API docs.

> **Electron / desktop app gotcha — error 153 on every video.**
> The Tool Store's renderer loads over `file://` in the packaged
> desktop app, which has no valid origin or referer. YouTube's player
> rejects embeds in that context and always returns error 153 — even
> for the canonical embeddable sample above. This is not a per-video
> setting; it is a transport-level restriction.
>
> Inside the desktop app, prefer one of:
>
> 1. **HTML5 `<video>` with a direct MP4/WebM URL** (section 5). This
>    always works because the browser fetches the file directly with
>    no third-party origin check. Recommended default for in-app video.
> 2. **Vimeo embed** (section 7). Vimeo's player is more permissive
>    about `file://` origins than YouTube's and typically renders.
> 3. **Open YouTube links externally.** Link to the YouTube URL with a
>    regular `<a target="_blank">` and let the user's browser handle it.
>
> The YouTube iframe pattern below still works fine in a normal browser
> (web build, dev server), so it is kept for reference — but expect it
> to fail inside the packaged Electron app.

## 7. Vimeo embed

<div style="position: relative; width: 100%; max-width: 720px; aspect-ratio: 16/9; margin: 1em auto;">
  <iframe
    src="https://player.vimeo.com/video/76979871"
    title="Sample Vimeo embed"
    style="position: absolute; inset: 0; width: 100%; height: 100%; border: 0; border-radius: 8px;"
    allow="autoplay; fullscreen; picture-in-picture"
    allowfullscreen
  ></iframe>
</div>

## 8. Audio

Direct audio files use `<audio controls>`:

<audio
  src="https://upload.wikimedia.org/wikipedia/en/4/4a/Commodore_64_theme.ogg"
  controls
  preload="metadata"
  style="display: block; margin: 1em 0;"
></audio>

## 9. SVG

SVG renders inline or via `<img src="...svg">`. Inline gives you styling
and animation hooks:

<svg viewBox="0 0 240 80" width="240" height="80" xmlns="http://www.w3.org/2000/svg" style="display:block; margin: 1em auto;">
  <rect x="0" y="0" width="240" height="80" rx="8" fill="#0f766e" />
  <text x="120" y="50" text-anchor="middle" fill="white" font-family="system-ui" font-size="20" font-weight="600">Inline SVG</text>
</svg>

## 10. Image gallery / multi-column

Use a CSS grid via a `<div>` wrapper. The blog's prose CSS doesn't fight
arbitrary HTML layout.

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 0.5rem; margin: 1em 0;">
  <img src="https://picsum.photos/seed/g1/400/300" alt="Gallery 1" style="width:100%; height:auto; border-radius: 6px; margin:0;" />
  <img src="https://picsum.photos/seed/g2/400/300" alt="Gallery 2" style="width:100%; height:auto; border-radius: 6px; margin:0;" />
  <img src="https://picsum.photos/seed/g3/400/300" alt="Gallery 3" style="width:100%; height:auto; border-radius: 6px; margin:0;" />
  <img src="https://picsum.photos/seed/g4/400/300" alt="Gallery 4" style="width:100%; height:auto; border-radius: 6px; margin:0;" />
</div>

## 11. Animated GIF

GIFs are just images, no special treatment:

<img src="https://media.giphy.com/media/3o7TKr3nzbh5WgCFxe/giphy.gif" alt="Animated placeholder" style="display:block; margin: 1em auto; max-width: 360px; border-radius: 6px;" />

## 12. Figure with caption

Use `<figure>` + `<figcaption>` for an inline caption tied to the image:

<figure style="margin: 1em 0; text-align: center;">
  <img src="https://picsum.photos/seed/figure/800/450" alt="A landscape with a caption" style="width: 100%; max-width: 720px; height: auto; border-radius: 8px; margin: 0;" />
  <figcaption style="font-size: 0.85em; color: var(--muted-foreground); margin-top: 0.4em;">
    Figure 12. A landscape photo with an HTML &lt;figcaption&gt; caption.
  </figcaption>
</figure>

## What's NOT supported (currently)

- **Wiki-style `[[image-name]]` shorthand** — use standard markdown or HTML.
- **CommonMark image-with-title `![alt](url "title")`** — the title attribute renders
  on hover but isn't visually surfaced.
- **Server-side image optimization** — images load directly from the
  source URL. For large images, consider hosting them on a CDN that
  supports resize query params.

## Security note

Because raw HTML is passed through, **only add sources you trust as
blog GitHub repo / Gist sources**. A hostile remote markdown file could
ship `<script>` tags. CMS-authored posts go through the same renderer
and are subject to the same caveat. This is fine for self-managed
content — flag it if you ever expose user-submitted sources.
