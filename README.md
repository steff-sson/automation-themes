# automation-themes

Shared Tailwind theme + Jinja2 components for automation web apps.
Notion-inspired dark mode — warm, technical, human.

## What's inside

- `css/theme.css` — `@import "tailwindcss"` + `@theme` (warm dark palette)
- `templates/base.html` — Sidebar-Layout with `{% block nav_items %}` (flexible per app)
- `templates/_components.html` — Jinja2 macros: `nav_item()`, `status_dot()`, `section_header()`, `metric_card()`

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
{% from "automation-themes/templates/_components.html" import nav_item, section_header, status_dot, metric_card %}

{% block sidebar_header %}⚡ livestream-recorder{% endblock %}

{% block nav_items %}
  {{ nav_item("/", "📊", "Dashboard", active=true) }}
  {{ nav_item("/config", "⚙️", "Config") }}
{% endblock %}

{% block sidebar_footer %}livestream-recorder v0.1.0{% endblock %}

{% block content %}
  {{ section_header("Recording") }}
  {{ metric_card("Status", "Recording", status="#22c55e") }}
  {{ metric_card("Duration", "01:23:45") }}
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
| `bg` | `#0f0f0f` | Page background |
| `surface` | `#1a1a1a` | Cards, sidebar, inputs |
| `glass` | `rgba(26,26,26,0.7)` | Overlays, frosted effects |
| `border` | `#2a2a2a` | Dividers, subtle borders |
| `text` | `#e8e8e8` | Primary text |
| `text-muted` | `#9ca3af` | Secondary text |
| `accent` | `#3b82f6` | Links, interactive elements |
| `success` | `#22c55e` | OK status |
| `error` | `#ef4444` | Error status |
| `warning` | `#f59e0b` | Warning status |
