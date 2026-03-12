---
title: 'Gute Commit Messages schreiben'
description: 'Warum "fix stuff" nicht reicht und wie es besser geht.'
pubDate: 2026-02-20
---

Jeder hat schon mal `git log` geöffnet und eine Wand aus "fix", "update", "wip" gesehen. Das ist nicht nur hässlich — es ist verlorenes Wissen.

## Das Problem

Eine Commit Message beantwortet eine Frage: **Warum wurde diese Änderung gemacht?** Nicht was geändert wurde (das zeigt der Diff), sondern warum.

Schlecht:
```
fix bug
```

Besser:
```
Verhindere doppelte API-Aufrufe beim Seitenwechsel

Der useEffect-Hook wurde bei jedem Render neu ausgelöst,
weil die Dependency-Liste fehlte. Das führte zu Race
Conditions bei schnellem Navigieren.
```

## Meine Regeln

1. **Imperativ in der ersten Zeile.** "Füge hinzu", nicht "Hinzugefügt". Liest sich wie ein Befehl an die Codebase.
2. **Erste Zeile unter 72 Zeichen.** Wird in `git log --oneline` abgeschnitten.
3. **Leerzeile, dann der Kontext.** Warum, nicht was. Welches Problem wird gelöst? Was war die Alternative?
4. **Ticket-Referenz am Ende.** `Fixes #423` oder `Ref: PROJ-187`.

## Wann es egal ist

Beim Experimentieren auf einem Feature-Branch. Da darf es chaotisch sein. Vor dem Merge wird mit `git rebase -i` aufgeräumt.

Commit Messages sind Briefe an dein zukünftiges Ich. Sei nett zu dir.
