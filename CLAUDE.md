# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

The 3proxy.org website (`3proxy.ru`). A static HTML site for the 3proxy tiny free proxy server project. There is no server-side or client-side application code — just static content templates and assets.

## Build / Generate Pages

Pages use a **C preprocessor-based templating system** — `.html.in` files use `#include` and `#define` directives. To generate `.html` output from a template:

```sh
cpp -P -undef -Wundef -std=c99 -I. -I.. templates/index.html.in > index.html
```

Content is included from a sibling `3proxy` repository via `../3proxy/doc/...` paths, so both `3proxy.org` (or `3proxy.ru`) and `3proxy` repos need to be cloned side by side:

```
GitHub/
├── 3proxy.org/     # English site (this repo)
├── 3proxy.ru/      # Russian site
└── 3proxy/         # Doc content (doc/html/*.html)
```

## Templating Conventions

- **`#define Title "..."`** — sets the page `<title>`
- **`#include "include/intro.html"`** — page header (navbar, CSS, JS, opening HTML/body tags)
- **`#include "include/postpage.html"`** — page footer (closing tags, copyright)
- Templates include content from `include/` and sometimes reference `doc/html/` for inline content (e.g. faqe, howtoe).
- Placeholder variables like `3proxy@3proxy.ru`, `Yandex`, `0.0.0.0` are substituted during deployment (likely via sed or env vars).

## Project Structure

```
.
├── include/          # HTML fragments (intro.html = header, postpage.html = footer)
├── templates/        # .html.in template files organized by section
│   ├── index.html.in        # Landing page (news, release notes)
│   ├── documents.html.in    # Documentation index
│   ├── howtoe.html.in       # HowTo (English), includes doc/html/howtoe.html
│   ├── howtor.html.in       # HowTo (Russian)
│   ├── faqe.html.in         # FAQ (English)
│   ├── faqr.html.in         # FAQ (Russian)
│   ├── securityen.html.in   # Security considerations
│   ├── highload.html.in     # High load optimization
│   ├── download/            # Download pages (stable, devel)
│   ├── plugins/             # Plugin documentation (PCRE, SSL, Strings, Traffic, Transparent, WindowsAuth)
│   ├── doc/                 # Manual pages
│   └── donations/           # Donations page
├── style/            # Bootstrap CSS
├── js/               # jQuery and Bootstrap JS
├── images/           # Site images, icons, bullets, forum graphics
└── robots.txt        # Robots config with Yandex placeholder
```

## Key Patterns

- **Two languages**: English and Russian pages, suffix `e` = English, `r` = Russian (e.g. `howtoe.html.in`/`howtor.html.in`, `faqe.html.in`/`faqr.html.in`)
- **Copyright year**: Footer spans `2000-2026` — update the end year when needed (in `include/postpage.html`)
- **Release announcements**: New versions are announced on `templates/index.html.in` in the "Hot news" table
- **Placeholder variables**: `3proxy@3proxy.ru` (email contact), `Yandex` (org name), `0.0.0.0` (additional contact) — these are replaced at deploy time
