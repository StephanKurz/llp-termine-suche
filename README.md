# Termine-Suche (Easyverein)

Ein schlankes, per `<iframe>` einbettbares Widget, um alle Termine im Easyverein-Vereinskonto
nach Stichwort zu durchsuchen – inklusive Teilnehmerzahlen und -namen.

**Live:** https://stephankurz.github.io/llp-termine-suche/

## Einbindung

```html
<iframe
  src="https://stephankurz.github.io/llp-termine-suche/"
  style="width:100%; height:600px; border:0;">
</iframe>
```

Es gibt keinen eigenen Login: Die einbettende Webseite ist die Sicherheitsgrenze (gleiches
Prinzip wie beim Schulkontakte-Editor).

## Funktionen

- **Volltextsuche** über Titel, Ort, Beschreibung und Notiz aller Termine (Easyverein-API-Parameter
  `search`), Treffer absteigend nach Datum sortiert.
- **"Weitere Treffer laden"**, da Easyverein pro Seite nur 5 Treffer liefert.
- **"Auch künftige Termine anzeigen"** (Checkbox, standardmäßig aus): ohne Haken werden nur
  vergangene/heutige Termine gezeigt (blendet z. B. weit in der Zukunft liegende automatische
  Erinnerungstermine aus); mit Haken auch alle künftigen.
- **Klickbarer Termin-Titel**: öffnet den Termin direkt in Easyverein
  (`https://easyverein.com/public/<Verein>/calendar/<Termin-ID>`, führt bei Bedarf zunächst durch
  den Login).
- **Teilnehmerzahl direkt sichtbar**: jeder Treffer zeigt automatisch "N Teilnehmer anzeigen"
  (bzw. ausgegraut "Keine Teilnehmer" bei 0), ohne dass man erst klicken muss.
- **Teilnehmerliste per Klick**: zeigt die einzelnen Namen, gruppiert nach Status (Kommt / Kommt
  vielleicht / Wurde eingeladen / Kommt nicht / Teilgenommen / Nicht teilgenommen).
- **Kursleiter-Erkennung**: Enthält der Kommentar einer Teilnahme das Wort "Kursleiter"
  (o. Ä. wie "Kursleiterin", "Kursleitung" – Erkennung ist nicht groß-/kleinschreibungsabhängig),
  wird die Person weiterhin in der Liste angezeigt (mit Kommentar in Klammern hinter dem Namen),
  aber **nicht mitgezählt**. Beispiel: 6 Teilnahmen, davon 2 Kursleiter → Zähler zeigt "4
  Teilnehmer", Liste zeigt alle 6 inkl. der beiden Kursleiter.

## Architektur

Statisches HTML (`index.html`, kein Build-Schritt, keine Abhängigkeiten) plus zwei Supabase Edge
Functions im Projekt `llp-schuldaten`, die den Easyverein-API-Key serverseitig halten (er darf nie
im Browser landen):

| Edge Function | Zweck |
|---|---|
| `search-events` | Volltextsuche, liefert Titel/Datum/Ort je Treffer |
| `event-participation` | Schneller Zähler + Status-Breakdown für die automatische Anzeige "N Teilnehmer anzeigen" (ohne Namen, ohne Kursleiter) |
| `event-participants` | Vollständige Namensliste inkl. Status und Kursleiter-Kommentar, wird erst beim Aufklappen geladen |

Beide Teilnehmer-Funktionen lösen Namen über `contact-details?id__in=...` auf und geben
ausschließlich Name + Status zurück – keine weiteren Kontaktdaten (Telefon, E-Mail, Adresse,
Geburtsdatum) verlassen die Funktion.

**Hinweis Namensanzeige:** Namen werden immer angezeigt, unabhängig vom easyVerein-eigenen
Mitglieder-Flag "Name im Mitgliederbereich anzeigen" (`showName`) – bewusste Entscheidung, da
dieses Tool für den Vorstand gedacht ist, der ohnehin vollen Zugriff im Easyverein-Adminbackend
hat.

## Repo

https://github.com/StephanKurz/llp-termine-suche (öffentlich, GitHub Pages aus `main`/`/`)
