# automation-themes

Shared Tailwind theme + Jinja2 components for automation web apps.
Bright-only design with warm, technical palette.

## What's inside

- `css/theme.css` — `@import "tailwindcss"` + `@theme` (warm bright palette)
- `templates/base.html` — Sidebar layout with `{% block nav_items %}` (flexible per app)
- `templates/_components.html` — Jinja2 macros: `nav_item()`, `status_badge()`, `section_header()`

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
{% from "automation-themes/templates/_components.html" import nav_item, section_header, status_badge %}

{% block sidebar_header %}automation{% endblock %}

{% block nav_items %}
  {{ nav_item("/", "layout-dashboard", "Dashboard", active=true) }}
  {{ nav_item("/config", "settings", "Config") }}
{% endblock %}

{% block sidebar_footer %}my-app v1.0.0{% endblock %}

{% block content %}
  {{ section_header("Section Title", "icon-name") }}
  <div class="bg-white rounded-xl p-6 border border-border shadow-md">
    Content here
  </div>
{% endblock %}
```

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
COPY --from=build /app/static/css/output.css ./static/css/output.css
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

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
| `--color-success` | `#81B29A` | OK status, save buttons |
| `--color-error` | `#dc2626` | Error status |
| `--color-warning` | `#f59e0b` | Warning status |

## Nav Items

Use Lucide icons (not emojis). Nav items close sidebar on mobile click.

```jinja2
{{ nav_item("/path", "icon-name", "Label", active=true) }}
```
