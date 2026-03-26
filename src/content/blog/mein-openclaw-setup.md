---
title: 'Mein OpenClaw Setup: Ein KI-Assistent auf dem eigenen Server'
description: 'Wie ich mir mit OpenClaw einen persönlichen KI-Assistenten auf einem VPS eingerichtet habe — erreichbar über Telegram, mit Zugriff auf Kalender, Einkaufsliste, E-Mails und einer geteilten Obsidian Vault als Single Source of Truth.'
pubDate: 2026-03-26
---

*"Setz Hafermilch auf die Einkaufsliste und was steht morgen im Kalender an?"* — Telegram-Nachricht raus, drei Sekunden später die Antwort. Kein App-Wechsel, kein manuelles Nachschauen. So sieht mein Alltag aus, seitdem ich **OpenClaw** auf einem eigenen Server laufen habe.

## Was ist OpenClaw?

OpenClaw ist ein Open-Source Agent-Framework. Du installierst es auf einem Server, verbindest ein LLM (bei mir Claude von Anthropic) und gibst dem Agenten über **MCP-Server** (Model Context Protocol) Zugriff auf externe Tools. Das Ergebnis: ein persönlicher Assistent, der nicht nur chattet, sondern tatsächlich Dinge erledigt.

## Hardware & Netzwerk

Ein kleiner VPS bei Hetzner — 4 CPU-Kerne, 4 GB RAM, Ubuntu 24.04. Reicht locker, die schwere Arbeit macht die Claude API in der Cloud.

Der Server hängt **nicht am offenen Internet**. Stattdessen läuft alles über **Tailscale** (Mesh-VPN, WireGuard-basiert). SSH ist der einzige öffentliche Port. Dazu TLS mit automatisch erneuerten Let's-Encrypt-Zertifikaten.

## Zwei Agenten, ein Server

Auf dem VPS laufen **zwei komplett getrennte OpenClaw-Instanzen** — jeweils mit eigenem Gateway, Telegram-Bot und systemd-Service.

**B-Sisstant** ist der persönliche Assistent. Läuft auf Claude Opus, erreichbar als Telegram-DM. Kann alles: Einkaufsliste, Kalender, Mails, Vault, Nextcloud.

**Projektbot** ist ein PM-Bot für einen Startup-Gruppenchat. Läuft auf dem günstigeren Claude Sonnet, liest im Chat mit und springt ein bei Tasks, Entscheidungen oder Deadlines. Eigener Telegram-Bot, eigener Port, eigene Vault.

Die Trennung hat handfeste Gründe: ein Crash betrifft den anderen nicht, die Modelle passen zum jeweiligen Einsatzzweck, und die Daten bleiben sauber getrennt — der Startup-Partner soll nicht in meine persönlichen Notizen schauen können.

## Was die Bots können

Über MCP-Server hat der Agent Zugriff auf:

- **Bring! Einkaufsliste** — *"Pack Tomaten und Mozzarella auf die Liste"* — erledigt. Auch abhaken und entfernen.
- **Google Workspace** — Kalender, Gmail, Drive. Termine erstellen, Mails lesen, Dateien verwalten.
- **Nextcloud** — Dateien ablegen und abrufen, geteilter Ordner über Tailscale.

### Gmail-Triage

Eins meiner Lieblings-Features. Alle 30 Minuten (7–23 Uhr) durchsucht ein eigener **Triage-Agent** (Haiku-Modell, ~2 Cent/Tag) meine ungelesenen Mails. Spam und Newsletter werden still als gelesen markiert, relevante Mails bekomme ich als Zusammenfassung auf Telegram. Der Agent hat nur Lesezugriff, kann keine Mails senden, und Anweisungen aus E-Mail-Bodies werden ignoriert (Prompt-Injection-Schutz).

## Die Obsidian Vault als Single Source of Truth

Das wichtigste Puzzleteil im ganzen Setup.

Ich habe mehrere KI-Instanzen: B-Sisstant und Projektbot auf dem OpenClaw-VPS, dazu Claude-Code-Agenten auf einem zweiten Server für Coding-Projekte. Jede Instanz ist komplett eigenständig — aber alle brauchen Zugriff auf dasselbe Wissen.

Die Lösung: eine zentrale **Obsidian Vault**, die über **obsidian-headless** (ein headless Obsidian-Sync-Client als systemd-Service) bidirektional und continuous auf jeden Server synchronisiert wird.

```
Alex (Desktop / iOS)
    ↕ Obsidian Sync
Zentrale Vault (ai_vault)
    ↕ obsidian-headless        ↕ obsidian-headless
OpenClaw VPS (B-Sisstant)    Coding-Server (Claude Code)
```

Wenn B-Sisstant eine Entscheidung dokumentiert, kann der Claude-Code-Agent sie sofort lesen. Wenn ich am Handy eine Notiz schreibe, ist sie Sekunden später für alle Agenten verfügbar. Und umgekehrt.

### Warum Obsidian Sync?

Git erfordert Commits und Pushes — Reibung, die ein Agent nicht zuverlässig handhabt. Eine Datenbank wäre ein weiterer Dienst, und ich könnte die Daten nicht einfach am Handy lesen. Obsidian Sync trifft den Sweet Spot: Markdown-Dateien, die jeder Agent direkt lesen und schreiben kann, bidirektionaler Sync mit Conflict-Merge, kein Server-Setup, und jeder Agent hat eine lokale Kopie.

### Struktur statt Chaos

Damit das funktioniert, gibt es klare Konventionen: Frontmatter mit Tags und Typ, feste Ordnerstruktur (`projects/`, `knowledge/`, `daily/`), ein automatisch generiertes `INDEX.md` das die Agenten bei jedem Session-Start laden. Eine lokale Suchmaschine (**QMD**) indexiert alles alle 5 Minuten — Vault, Workspace und Chatverläufe durchsuchbar in unter einer Sekunde.

Der Projektbot hat bewusst eine **eigene Vault** mit separater Sync-Verbindung. Gleiche Architektur, aber sauber getrennte Datenräume.

## Self-Healing

Ein Assistent bringt nur was, wenn er zuverlässig läuft:

- **Watchdog** — prüft alle 2 Minuten per TCP ob das Gateway antwortet, sonst automatischer Neustart
- **Nightly Restart** — täglich um 4 Uhr präventiver Neustart
- **Memory-Limits** — harte Obergrenzen per systemd gegen Memory-Leaks

## Gedächtnis

Neben der Vault hat der Agent ein eigenes Memory-System: tägliche Logs, ein Langzeitgedächtnis für sessionübergreifende Fakten, und einen **Self-Improving Skill** der aus Korrekturen lernt. Alle Chatverläufe werden stündlich als Markdown exportiert und durchsuchbar gemacht.

## Was es kostet

- **VPS** — ein paar Euro/Monat
- **Claude API** — variiert (Opus für Hauptchat, Haiku/Sonnet für Triage und Projektbot)
- **Obsidian Sync** — 5 €/Monat
- **Whisper API** — minimal, für Sprachnachrichten

## Fazit

Statt zehn verschiedene Apps schreibe ich eine Telegram-Nachricht. Der Bot kauft ein, liest meine Mails, verwaltet meinen Kalender und dokumentiert alles in einer geteilten Obsidian Vault.

Die größte Erkenntnis: Die KI-Infrastruktur ist nur so gut wie das Wissensmanagement dahinter. Die Agenten sind austauschbar, die Vault bleibt.

OpenClaw ist Open Source, ein kleiner VPS reicht, und die MCP-Server kannst du schrittweise ergänzen. Fragen? Schreib mir gerne.
