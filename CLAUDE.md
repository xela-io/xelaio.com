# over-cloud.de

Minimalistischer Astro-Blog auf https://over-cloud.de

## Tech Stack

- **Astro 6** mit MDX, Sitemap, RSS
- **View Transitions** (Astro ClientRouter) für sanfte Seitenübergänge
- **Dark/Light Theme** mit System-Erkennung und Toggle
- **Docker** (multi-stage: node build → nginx serve)
- **Caddy** als Reverse Proxy mit automatischem TLS
- **Cloudflare** DNS + Proxy (SSL/TLS: Full Strict)

## Projektstruktur

```
src/
  content/blog/   ← Artikel als .md/.mdx Dateien
  components/      ← BaseHead, Header, Footer, FormattedDate
  layouts/         ← BlogPost.astro
  pages/           ← index (Post-Liste), about, blog/[...slug], rss.xml
  styles/          ← global.css (Farbpalette, Dark/Light Variablen)
```

## Neuen Artikel schreiben

Neue `.md` Datei in `src/content/blog/`:

```yaml
---
title: 'Titel'
description: 'Kurzbeschreibung (wird in der Post-Liste angezeigt)'
pubDate: 2026-03-12
---
```

## Farbpalette

- `--grey: #BFC6C4`
- `--cream: #E8E2D8`
- `--green: #6F8F72`
- `--orange: #F2A65A`

## Lokale Entwicklung

```bash
npm run dev      # Dev-Server auf localhost:4321
npm run build    # Static Build nach dist/
```

Benötigt Node 22+ (via nvm).

## Deployment

Server: `xela@46.224.208.7`

```bash
# Dateien auf Server kopieren
scp -r src public Dockerfile nginx.conf docker-compose.yml package*.json astro.config.mjs tsconfig.json xela@46.224.208.7:~/over-cloud.de/

# Auf dem Server bauen und starten
ssh xela@46.224.208.7 "cd ~/over-cloud.de && docker compose build --no-cache && docker compose up -d"
```

Der Container verbindet sich automatisch mit dem Caddy-Netzwerk (`matrix_matrix-external`) via docker-compose.yml. Caddy-Config liegt in `/opt/matrix/Caddyfile` auf dem Server.

## Git

- Repo: `git@github.com:xela-io/over-cloud.de.git`
- Branch: `main`
