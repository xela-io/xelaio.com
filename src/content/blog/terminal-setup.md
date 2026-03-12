---
title: 'Mein Terminal-Setup 2026'
description: 'Die Tools, mit denen ich jeden Tag arbeite.'
pubDate: 2026-03-05
---

Ich verbringe den Großteil meines Tages im Terminal. Über die Jahre hat sich ein Setup eingependelt, das ich selten anfasse.

## Shell

Zsh mit einer minimalen `.zshrc`. Oh My Zsh hab ich irgendwann rausgeworfen — zu langsam, zu viel Magie. Stattdessen ein paar handverlesene Plugins über zinit:

- `zsh-autosuggestions` — Vorschläge aus der History
- `zsh-syntax-highlighting` — Fehler sehen bevor man Enter drückt
- `starship` als Prompt — schnell, informativ, konfigurierbar

## Editor

Neovim. Nicht weil es hip ist, sondern weil ich nach zehn Jahren die Tastenkombinationen in den Fingern habe. LSP für TypeScript und Go, sonst wenig Plugins.

## Alltagstools

| Tool | Zweck |
|------|-------|
| `ripgrep` | Suchen |
| `fd` | Dateien finden |
| `bat` | Besseres `cat` |
| `jq` | JSON verarbeiten |
| `lazygit` | Git-UI im Terminal |

## Was ich bewusst nicht nutze

Tmux. Ich weiß, Kontroverse. Aber seit ich einen tiling Window Manager benutze, brauche ich kein Session-Management im Terminal. Ein Fenster pro Aufgabe reicht.

Die beste Konfiguration ist die, über die man nicht mehr nachdenkt.
