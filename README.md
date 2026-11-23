# Bremer Grundschulen kennenlernen – Kontaktformular

Handyfreundliches Kontaktformular für angehende Lehrkräfte, die nach dem Referendariat Bremer Grundschulen kennenlernen möchten. Interessierte kreuzen eine oder mehrere Grundschulen an und hinterlassen Name, Telefon oder E-Mail sowie optional eine Nachricht. Jede ausgewählte Schule bekommt daraufhin eine eigene E-Mail und kann direkt Kontakt aufnehmen.

Anlass ist die Veranstaltung für Absolvent:innen des Referendariats in Hamburg am 24.11.2026, auf der Bremen mit einem Stand vertreten ist. Das Formular wird dort per QR-Code (Plakat) aufgerufen.

Verantwortlich: Referat 14 „Personalmanagement an Schulen“, Der Senator für Kinder und Bildung, Freie Hansestadt Bremen – [referat14@bildung.bremen.de](mailto:referat14@bildung.bremen.de)

## Aufruf

Die Seite wird über GitHub Pages ausgeliefert:

- Formular: <https://skb-referat10.github.io/schulen-in-bremen/kontakt.html>
- Mit vorab angekreuzter Schule (z. B. für einen QR-Code je Schule):
  `…/kontakt.html?schule=014`
- Mehrere Schulen vorab ankreuzen: `…/kontakt.html?schule=014,040`

Ohne Parameter erscheint die vollständige Schulliste, gegliedert nach Regionen.

## Inhalt des Repositorys

| Datei | Zweck |
| --- | --- |
| `kontakt.html` | Das komplette Formular: HTML, CSS und JavaScript in einer Datei |
| `kontakt-logo-skb.png` | Logo „Der Senator für Kinder und Bildung“ (Kopfzeile) |
| `kontakt-logo-bremen.png` | Logo „Bremen Bremerhaven“ (Fußzeile) |

Die Seite ist rein statisch. Der eigentliche Mailversand läuft über ein PHP-Skript auf einem separaten Webspace (siehe unten) und ist **nicht** Teil dieses Repositorys.

## So funktioniert es

```
QR-Code / Link  →  kontakt.html (GitHub Pages)  →  mail.php (PHP-Webspace)  →  E-Mail an jede ausgewählte Schule
```

1. Die Seite prüft die Eingaben direkt im Browser (mindestens eine Schule, Name, Telefon oder E-Mail, Einwilligung).
2. Das Formular wird per `fetch` an den in `CONFIG.ENDPOINT` hinterlegten Versand-Endpunkt geschickt.
3. Das Skript auf dem Server kennt die E-Mail-Adressen der Schulen und verschickt je Schule eine Nachricht.
4. Die Seite zeigt eine Bestätigung mit den Namen der ausgewählten Schulen.

## Anpassen

Alle Einstellungen stehen am Anfang des `<script>`-Blocks in `kontakt.html` im Objekt `CONFIG`. Nur dort etwas ändern:

- `ENDPOINT` – Adresse des Versand-Skripts. **Leer lassen = Testmodus**: Es wird nichts verschickt, die Bestätigung zeigt einen gelben Hinweis.
- `IMPRESSUM`, `DATENSCHUTZ` – Links in der Fußzeile. Leer = Link wird ausgeblendet.
- `SCHULEN` – Liste der Schulen mit `nr` (Schulnummer), `name`, `region` und `ort`. Die Reihenfolge entspricht der Anzeige; ein Wechsel bei `region` erzeugt eine Zwischenüberschrift.

**Wichtig:** In `kontakt.html` stehen bewusst **keine E-Mail-Adressen**. Die Adressen der Schulen liegen ausschließlich in den Einstellungen des Versand-Skripts auf dem Server. Wer eine Schule ergänzt, muss sie daher an beiden Stellen mit derselben Schulnummer eintragen.

Änderungen am Branch `main` sind nach kurzer Zeit online. Falls nicht: Inkognito-Fenster oder Hard-Reload (Cmd/Strg + Shift + R).

## Versand-Skript (Server)

Der Versand liegt auf einem PHP-Webspace in Deutschland und ist in `ENDPOINT` eingetragen. Das Skript

- nimmt nur Anfragen von dieser Seite an (Herkunftsprüfung, die GitHub-Pages-Adresse muss dort freigegeben sein),
- enthält die Schuladressen und optional SMTP-Zugangsdaten,
- hat einen Testmodus, in dem alle Mails an eine Testadresse statt an die Schulen gehen,
- führt ein geschütztes Versandprotokoll.

Hinweis aus den Tests: Behördenpostfächer (z. B. `@schulverwaltung.bremen.de`, `@bildung.bremen.de`) stellen Mails vom Webspace teils verspätet zu oder sortieren sie als Spam ein. Zuverlässiger ist der Versand über ein echtes Postfach per SMTP; außerdem hilft es, wenn die Schulen den Absender als sicher markieren.

## Schutz vor Spam

- Unsichtbares Honigtopf-Feld (`website`): Bots, die es ausfüllen, sehen eine Bestätigung, es wird aber nichts verschickt.
- Die Ausfülldauer wird mitgeschickt; zu schnelle Absendungen werden serverseitig verworfen.
- Begrenzung der Meldungen pro Stunde und Herkunftsprüfung auf dem Server.

## Datenschutz

- Die Angaben gehen per E-Mail an die Schulleitungen der ausgewählten Schulen und dienen nur der Kontaktaufnahme.
- Zusätzlich werden sie in einem geschützten Versandprotokoll gespeichert und dort nach spätestens 90 Tagen automatisch gelöscht.
- Die Einwilligung kann jederzeit per E-Mail an [referat14@bildung.bremen.de](mailto:referat14@bildung.bremen.de) widerrufen werden.
- Die Seite verwendet keine externen Schriften, kein Tracking und keine Cookies. `noindex` verhindert die Aufnahme in Suchmaschinen.

Vor dem Livebetrieb sollten die Links zu Impressum und Datenschutzerklärung in `CONFIG` eingetragen werden.
