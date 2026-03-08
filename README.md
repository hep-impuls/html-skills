# HTML Skills fuer Lehrpersonen

Willkommen. Dieses Repository enthaelt sofort nutzbare Bildungs-HTML-Beispiele und ein wiederverwendbares Codex-Skill-Paket fuer Lehrpersonen.

## Worum es in diesem Repo geht

Dieses Projekt ist eine kleine oeffentliche Ressource fuer Lehrpersonen, die:

- verschiedene Stile von unterrichtstauglichen HTML-Seiten erkunden moechten
- bewaehrte Seitenstrukturen fuer Lektionen und Arbeitsblaetter wiederverwenden moechten
- neue Seiten mit einem skillbasierten Workflow erstellen moechten

Die Beispiele orientieren sich an schweizerischen Berufsbildungs-/ABU-Kontexten, lassen sich aber auf andere Faecher uebertragen.

## Struktur des Repositories

- `examples/type_A.html`
  Interaktives Lektionenformat (Slides, Aktivitaeten, Punkte, Druckansicht).
- `examples/type_B.html`
  Druckorientiertes A4-Arbeitsblatt-/Dokumentformat.
- `examples/type_C.html`
  Scrollbares Info-Dossier-Format (medienreich, screen-first).
- `examples/prompts.html`
  Copy-Paste-Seite fuer Lehrpersonen mit 8 Prompt-Vorlagen und Kopier-Buttons.
- `prompts.md`
  Prompt-Sammlung im Markdown-Format (gleicher Inhalt wie die HTML-Copy-Seite).
- `skill/edu-html-generator/edu-html-generator/SKILL.md`
  Hauptanleitung des Skills zur Generierung vollstaendiger Bildungs-HTML-Seiten.
- `skill/edu-html-generator/edu-html-generator/references/`
  Komponenten-Referenzen fuer die Seitentypen A, B und C.
- `edu-html-generator.zip`
  Paketierte Version des Skills fuer einfaches Teilen/Importieren.

## So verwendest du das Repo

1. Oeffne eine Datei aus `examples/` im Browser, um das jeweilige Format anzusehen.
2. Waehle das Format passend zu deinem Unterrichtsziel:
   - interaktive Schuelerarbeit -> Typ A
   - druckbares Arbeitsblatt/Broschuere -> Typ B
   - Ueberblick/Info-Dossier -> Typ C
3. Fuer schnelle Prompt-Nutzung oeffne `examples/prompts.html` und kopiere die passende Vorlage.
4. Nutze die Skill-Dateien in `skill/...` als Vorlage, um neue themenspezifische Seiten zu erzeugen.

## Prompts (neu)

- Interaktive Copy-Seite: `examples/prompts.html`
- Markdown-Quelle: `prompts.md`

Die Prompt-Seite ist fuer Lehrpersonen gedacht, die direkt im Unterrichtsalltag mit Copy/Paste arbeiten:

- pro Prompt ein eigener Kopier-Button
- optional "Alle Prompts kopieren"
- Hell/Dunkel-Umschalter und Druckansicht

## Fuer wen ist das gedacht?

- ABU- und Berufsfachschul-Lehrpersonen
- Sprachlehrpersonen mit interaktiven oder druckbasierten Materialien
- Lehrpersonen, die mit Single-File-HTML-Lernmaterialien arbeiten moechten

## Hinweise

- Die Beispiele sind eigenstaendige HTML-Dateien (kein Build-Prozess noetig).
- Einige Beispiele laden Webfonts und CDNs; fuer volle Darstellung kann Internetzugang noetig sein.
- Du kannst dieses Repo forken und fuer deine Klassen, Themen und Niveaus anpassen.
