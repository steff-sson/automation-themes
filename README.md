# automation-themes

Shared Tailwind theme + Jinja2 components for automation web apps.
Bright-only design with warm, technical palette.

## What's inside

- `css/theme.css` — `@import "tailwindcss"` + `@theme` (warm bright palette)
- `templates/base.html` — Sidebar layout with blocks for customization
- `templates/_components.html` — Jinja2 macros (see below)

## Installation

```bash
npm install github:steff-sson/automation-themes
```

## Usage

### CSS

Create `static/css/style.css` in your app:

```css
@import "automation-themes/css/theme.css";
```

Build Tailwind:

```bash
npx @tailwindcss/cli -i static/css/style.css -o static/css/output.css --minify
```

### Templates

```jinja2
{% extends "automation-themes/templates/base.html" %}
{% from "automation-themes/templates/_components.html" import nav_item, status_badge, card, input, btn %}

{% block sidebar_header %}my-app{% endblock %}
{% block sidebar_icon %}zap{% endblock %}

{% block nav_items %}
  {{ nav_item("/", "layout-dashboard", "Dashboard", active=true) }}
  {{ nav_item("/config", "settings", "Config") }}
{% endblock %}

{% block sidebar_footer %}my-app v1.0.0{% endblock %}

{% block content %}
  {% call card() %}
    <h2 class="text-base font-semibold text-text mb-3">Title</h2>
    {{ input("Label", "field_name", placeholder="...") }}
    {{ btn("Submit") }}
  {% endcall %}
{% endblock %}
```

### Blocks (override in your base.html)

| Block | Default | Purpose |
|-------|---------|---------|
| `title` | `Automation` | Page title |
| `mobile_icon` | `zap` | Lucide icon name (mobile header) |
| `mobile_header` | `automation` | Mobile header text |
| `sidebar_icon` | `zap` | Lucide icon name (sidebar) |
| `sidebar_header` | `automation` | Sidebar title text |
| `nav_items` | — | Sidebar navigation links |
| `sidebar_footer` | — | Version / footer text |
| `head` | — | Extra CSS/JS in `<head>` |
| `content` | — | Main page content |
| `scripts` | — | Extra JS at end of `<body>` |

### Docker

```dockerfile
FROM node:20-alpine AS build
WORKDIR /build
COPY package*.json .
RUN npm ci
COPY . .
RUN npx @tailwindcss/cli -i ./static/css/style.css -o ./static/css/output.css --minify

FROM python:3.12-slim
WORKDIR /app
COPY --from=build /build/static/css/output.css ./static/css/output.css
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

## Macros

### Layout

| Macro | Usage |
|-------|-------|
| `nav_item(href, icon, label, active)` | Sidebar navigation link |
| `card(class="")` | Card container (use with `{% call card() %}`) |
| `section_header(title, icon)` | Section heading with icon |

### Forms

| Macro | Usage |
|-------|-------|
| `input(label, name, type, placeholder, required, value, class)` | Form input field |
| `btn(label, type, variant, class)` | Button. Variants: `primary` (default), `secondary`, `danger`, `success` |

### Data Display

| Macro | Usage |
|-------|-------|
| `status_badge(status, color)` | Colored status pill |
| `metric_card(label, value, status, icon)` | Key-value pair row |
| `gradient_card(title, description, icon, gradient, href)` | Gradient hero card |
| `stat_card(label, value, change, icon, gradient)` | Stat with trend indicator |

## Color Palette

| Token | Value | Use |
|-------|-------|-----|
| `--color-background` | `#F4F1DE` | Page background (warm cream) |
| `--color-surface` | `#ffffff` | Cards, sidebar, inputs |
| `--color-surface-light` | `#f8f6ee` | Hover states, secondary surfaces |
| `--color-border` | `#e6e0c5` | Dividers, subtle borders |
| `--color-text` | `#3D405B` | Primary text (dark navy) |
| `--color-text-muted` | `#7a7d94` | Secondary text |
| `--color-accent` | `#3D405B` | Links, interactive elements |
| `--color-orange` | `#E79B85` | Warm accent (nav highlights) |
| `--color-success` | `#81B29A` | OK status |
| `--color-error` | `#dc2626` | Error status |
| `--color-warning` | `#f59e0b` | Warning status |
