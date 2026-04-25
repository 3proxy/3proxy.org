# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

One of the 3proxy website repos (`3proxy.org` — English, `3proxy.ru` — Russian). A static HTML site for the 3proxy tiny free proxy server project. Pages are generated from Jinja-like templates using `buildsite.py` from the sibling `3proxy-internal` repo.

## Build / Generate Pages

```sh
# From 3proxy-internal/
python3 buildsite.py ../3proxy ../3proxy.org
python3 buildsite.py ../3proxy ../3proxy.org ../3proxy.ru  # both at once
```

Repos layout (must be cloned side by side):

```
GitHub/
├── 3proxy.org/         # English site (this repo)
├── 3proxy.ru/          # Russian site
├── 3proxy/             # Doc content (doc/html/*.html, doc/*.rtf)
└── 3proxy-internal/    # Build script (buildsite.py)
```

## Template System

Templates use `.html.jinja` extension with a Jinja2-like syntax processed by Python (`buildsite.py` in `3proxy-internal/`).

### Directives

- **`{% set Title = "page title" %}`** — sets the `<title>` tag value
- **`{% include "path" %}`** — includes a file as raw text (resolved relative to template dir, site root, and `templates/`). Used for `include/intro.html` (header) and `include/postpage.html` (footer)
- **`{% content "path" %}`** — includes raw content from the doc content repo (`../3proxy/doc/html/...`)

### Template Examples

Simple page with inline content:
```jinja
{% set Title = "3proxy tiny free proxy server" %}
{% include "include/intro.html" %}

<h2>Page content here</h2>

{% include "include/postpage.html" %}
```

Page with external doc content:
```jinja
{% set Title = "Soft:3proxy:FAQ" %}
{% include "include/intro.html" %}
{% content "../3proxy/doc/html/faqe.html" %}
{% include "include/postpage.html" %}
```

The `{% content %}` path is resolved by substituting `../3proxy/` with the content repo path.

### Auto-generated doc templates

The build script auto-generates `doc/` templates for every `.html` file in the content repo's `doc/html/`, wrapping each with intro/postpage.html and a title "3proxy : Documentation : filename".

RTF files from the content repo's `doc/` are copied to the site's `doc/`.

### Output

Templates from `templates/` produce output at the site root (e.g. `templates/index.html.jinja` → `index.html`). Templates from `doc/` produce output inside `doc/`. The `.jinja` suffix is stripped.

## Project Structure

```
.
├── templates/
│   ├── include/               # HTML fragments (intro.html, postpage.html)
│   ├── index.html.jinja       # Landing page (news, release notes)
│   ├── documents.html.jinja   # Documentation index
│   ├── howtoe.html.jinja      # HowTo (English)
│   ├── howtor.html.jinja      # HowTo (Russian)
│   ├── faqe.html.jinja        # FAQ (English)
│   ├── faqr.html.jinja        # FAQ (Russian)
│   ├── securityen.html.jinja  # Security considerations
│   ├── highload.html.jinja    # High load optimization
│   ├── download/              # Download pages (stable, devel)
│   ├── plugins/               # Plugin docs (PCRE, SSL, Strings, Traffic, Transparent, WindowsAuth)
│   ├── doc/                   # Manual docs index
│   └── donations/             # Donations page
├── style/                     # Bootstrap CSS
├── js/                        # jQuery and Bootstrap JS
├── images/                    # Site images, icons, bullets, forum graphics
├── doc/                       # Generated: doc pages, manual pages, RTF files, plugins docs
├── robots.txt                 # Robots config with Yandex placeholder
└── *.html                     # Generated: all pages at site root
```

## Key Patterns

- **Two languages**: English and Russian pages, suffix `e` = English, `r` = Russian (e.g. `howtoe.html.jinja`/`howtor.html.jinja`)
- **Copyright year**: Footer spans `2000-2026` — update the end year when needed (in `templates/include/postpage.html`)
- **Release announcements**: New versions are announced on `templates/index.html.jinja` in the "Hot news" table
- **Placeholder variables**: `3proxy@3proxy.ru` (email contact), `Yandex` (org name), `0.0.0.0` (additional contact) — substituted at deploy time
