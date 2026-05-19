# Testresultate

## Übersicht

Für das Backend wurden insgesamt 71 Unit- und Integrationstests geschrieben. Als Framework verwenden wir Ruby on Rails mit Minitest. Die Tests decken alle wichtigen Schichten der Applikation ab: Controller, Services und Models. Zusätzlich gibt es noch einen separaten Lasttest mit k6.

Alle 71 Tests laufen durch, es gibt keine Fehlschläge.

---

## Controller Tests

### DLS Proxy Controller (7 Tests)

Der DLS Proxy Controller leitet Anfragen an den externen DRG Language Server weiter. Da dieser Server eine externe Abhängigkeit ist, haben wir `Net::HTTP` mit einem Stub ersetzt, damit die Tests nicht vom tatsächlichen Server abhängen.

Getestet wurde:
- Eine gültige Expression kommt als 200 zurück
- Der Controller leitet einen 400-Fehler des DLS-Servers korrekt weiter
- Fehlender oder leerer `expression`-Parameter gibt 400 zurück
- Timeout beim DLS-Server → 500 mit Fehlermeldung im Body
- Verbindungsabbruch (`ECONNRESET`) → 500
- Unerwartete Exception → 500 mit generischer Fehlermeldung

Alle 7 Tests bestehen.

---

### Filters Controller (10 Tests)

Das ist der Hauptendpunkt der Applikation. Hier werden die Filteranfragen aus dem Frontend entgegengenommen, an den `GetFilteredPatientsService` weitergegeben, und das Ergebnis zurückgeliefert. Zusätzlich wird in bestimmten Fällen ein Hintergrundjob (`PrepareDownloadJob`) angestossen, der eine CSV-Datei vorbereitet.

Getestet wurde:
- Happy Path: Der Controller gibt 200 mit dem korrekten JSON-Body zurück (inkl. `status`, `source`, `total_count`, `page`, `per_page`, `query_hash`)
- Service meldet `not_found` → 404 mit Fehlermeldung
- Service meldet einen anderen Fehler → 500 mit "Systemfehler"
- `PrepareDownloadJob` wird gestartet wenn noch keine CSV vorhanden ist
- Job wird **nicht** gestartet wenn CSV bereits angehängt ist
- Job wird **nicht** gestartet wenn bereits eine CSV generiert wird (`generating_csv: true`)
- `generating_csv` wird auf `true` gesetzt bevor der Job startet
- Alle Filter-Parameter werden korrekt an den Service weitergeleitet
- Kein Job wenn `filter_cache` nil ist
- Fehlender `filter`-Parameter → 400

Alle 10 Tests bestehen.

---

### Workspaces Controller (5 Tests)

Hier kann der Benutzer eine Liste aller verfügbaren Workspaces abrufen und einzelne Workspaces als ZIP exportieren. Im Gegensatz zu anderen Controller-Tests arbeitet dieser mit echten Datenbankeinträgen und ActiveStorage-Attachments, da das Datei-Handling direkt getestet werden soll.

Getestet wurde:
- `GET /api/workspaces` gibt eine Liste zurück, die den erstellten Workspace enthält
- Die Liste enthält `id` und `title`, aber kein `file`-Feld
- `GET /api/workspaces/:id/export` gibt die ZIP-Datei mit dem richtigen Content-Type zurück
- Export eines nicht existierenden Workspaces → 404
- Export wenn keine Datei angehängt ist → 404

Alle 5 Tests bestehen.

---

### Admin Settings Controller (5 Tests)

Über diesen Endpunkt können die Cache-Limits (wie viele Filter-Caches und Download-Caches gespeichert werden) abgerufen und verändert werden. Die Werte werden in der `SystemSetting`-Tabelle gespeichert.

Getestet wurde:
- `GET` gibt die aktuellen Limits zurück
- `PUT` mit beiden Werten aktualisiert beide Limits und gibt eine Bestätigungsmeldung zurück
- Wenn keine Settings in der DB vorhanden sind, werden die Default-Werte (100 / 10) zurückgegeben
- Nur `filter_cache_limit` senden → nur dieser wird aktualisiert
- Nur `download_cache_limit` senden → nur dieser wird aktualisiert

Alle 5 Tests bestehen.

---

### Admin Workspaces Controller (5 Tests)

Hier können Administratoren neue Workspaces hochladen oder bestehende löschen.

Getestet wurde:
- `POST /api/admin/workspaces` mit Titel und ZIP-Datei → 201 mit Bestätigungsmeldung
- Fehlender Titel → 422 mit Fehlerliste (enthält "Title")
- Fehlender `workspace`-Parameter komplett → 400 oder 422
- `DELETE /api/admin/workspaces/:id` löscht den Workspace und gibt 204 zurück
- Löschen eines nicht existierenden Workspaces → 404

Alle 5 Tests bestehen.

---

## Service Tests

### GetFilteredPatientsService (13 Tests)

Das ist der Kern der Anwendung. Der Service koordiniert den gesamten Such-Ablauf: Er prüft ob ein Cache-Treffer vorhanden ist, ruft sonst den DLS-Server auf, und fragt anschliessend die Parquet-Datei via DuckDB ab. Da dieser Service viele externe Abhängigkeiten hat (Datenbank, DLS-Server, ActiveStorage, DuckDB), werden alle diese mit Stubs ersetzt.

Getestet wurde:
- Cache Miss: DLS wird aufgerufen, Cache-Eintrag wird erstellt, Ergebnis kommt als `source: "dls_server"` zurück
- Cache Hit: DLS wird übersprungen, Ergebnis kommt als `source: "cache"` zurück
- Egin nicht gefunden → `not_found`-Fehler
- Workspace nicht gefunden → `not_found`-Fehler
- DLS liefert kein Array zurück → `bad_gateway`-Fehler
- Parquet-Datei nicht angehängt → `not_found`-Fehler
- Paginierungsparameter (`page`, `per_page`) werden korrekt an den `ParquetDuckService` weitergeleitet
- Sortierparameter (`sort_by`, `sort_dir`) werden korrekt weitergeleitet
- `FilterCache.cleanup_id_cache!` wird nach dem Erstellen eines neuen Cache-Eintrags aufgerufen
- Das Ergebnis enthält alle erwarteten Keys
- `server_cache_duration` ist eine nicht-negative Zahl
- `filter_cache` im Ergebnis ist das korrekte Cache-Objekt
- Leeres IDs-Array vom DLS → `total_count: 0`

Alle 13 Tests bestehen.

---

### ParquetDuckService (16 Tests)

Dieser Service liest Patientendaten aus einer Parquet-Datei mittels DuckDB. Da er direkt SQL generiert, war es wichtig, die SQL-Injection-Prüfung und die korrekte Query-Zusammensetzung zu testen. DuckDB wird mit einem Stub ersetzt, damit kein echtes Parquet-File nötig ist.

Getestet wurde:
- Normaler Aufruf gibt korrekte Zeilen zurück
- Leere `target_ids` → sofort leeres Array ohne DB-Anfrage
- Spaltenauswahl: Bei angegebenen Spalten wird kein `SELECT *` verwendet
- Kein `columns`-Argument → `SELECT *`
- Sortierung nach INTEGER-Spalte (z.B. `AlterInJahren`) mit CAST und richtiger Richtung
- Sortierung nach VARCHAR-Spalte (z.B. `Geschlecht`) mit CAST und richtiger Richtung
- Sortierung nach Datum (`Aufnahmedatum`) mit `STRPTIME`
- Unbekannte Spalte in `sort_by` → kein `ORDER BY` (Whitelisting)
- Ungültige `sort_dir` (SQL-Injection-Versuch) → Fallback auf `ASC`
- Paginierung: Seite 3, 10 pro Seite → `LIMIT 10 OFFSET 20`
- `per_page: nil` → kein `LIMIT` in der Query
- DuckDB-Fehler → leeres Array statt Exception
- Generischer Fehler → `{ error: "...", data: [] }`
- Ergebnis hat immer einen `:data`-Key
- WHERE-Klausel enthält alle übergebenen IDs
- Mehrere Zeilen werden korrekt als Array von Hashes gemappt

Alle 16 Tests bestehen.

---

## Model Tests

### FilterCache (8 Tests)

Das FilterCache-Model speichert die Resultate von Suchanfragen um wiederholte DLS-Aufrufe zu vermeiden. Zwei Cleanup-Methoden sorgen dafür, dass der Cache nicht unbegrenzt wächst. Diese Tests laufen gegen eine echte Test-Datenbank mit echten ActiveStorage-Blobs, da die Hash-Generierung die Datei-Checksums einbezieht.

Getestet wurde:
- `generate_hash` liefert bei gleichen Eingaben immer denselben Wert
- `generate_hash` liefert unterschiedliche Werte für unterschiedliche Logic Terms
- `generate_hash` gibt einen nicht-leeren String zurück
- `cleanup_id_cache!` tut nichts wenn Limit 0 ist
- `cleanup_id_cache!` tut nichts wenn die Anzahl Einträge unter dem Limit liegt
- `cleanup_id_cache!` löscht den ältesten Eintrag wenn das Limit überschritten wird
- `cleanup_id_cache!` lässt genau `limit` Einträge übrig
- `cleanup_download_cache!` löscht nur die CSV-Datei des ältesten Eintrags, der neueste bleibt

Alle 8 Tests bestehen.

---

### SystemSetting (2 Tests)

Einfaches Key-Value-Store für globale Einstellungen.

Getestet wurde:
- Wert speichern und wieder abrufen
- Bestehenden Wert überschreiben

Beide Tests bestehen.

---

## Datenbank Tests

Die EGIN-Dateien werden beim Upload automatisch in Parquet-Dateien umgewandelt. Da Parquet-Dateien read-only sind und direkt von DuckDB abgefragt werden, gibt es keine separate Datenbanklogik die getestet werden müsste. Getestet wurde stattdessen der Cache — also wie Suchergebnisse zwischengespeichert und wieder aufgeräumt werden. Das ist in den FilterCache-Tests abgedeckt.

---

## Frontend Tests (manuell)

### React UI

Das Frontend wurde manuell im Browser getestet. Da es sich um eine Webanwendung mit dynamischen Komponenten handelt, war manuelles Testen hier die pragmatischste Lösung.

Manuell überprüft wurde:
- Filter können erstellt und abgeschickt werden, Ergebnisse werden korrekt in der Tabelle angezeigt
- Paginierung und Sortierung funktionieren wie erwartet
- Fehlerzustände (z.B. kein Workspace ausgewählt) zeigen eine verständliche Meldung
- CSV-Download startet sobald die Datei bereit ist
- Workspace-Auswahl und Admin-Bereich (Upload, Löschen) funktionieren

Alle manuell getesteten Abläufe verhielten sich korrekt.

---

### Monaco Editor

Der Monaco Editor wird für die Eingabe von Filter-Ausdrücken verwendet. Auch dieser wurde manuell getestet.

Manuell überprüft wurde:
- Syntax-Highlighting und Autovervollständigung funktionieren
- Gültige Ausdrücke werden korrekt an das Backend weitergegeben
- Ungültige Ausdrücke zeigen eine Fehlermeldung aus dem DLS-Server
- Der Editor reagiert korrekt auf Eingaben und verhält sich wie erwartet

---

## Usability Tests (manuell)

Da es sich um eine kleine, übersichtliche UI handelt und die zukünftigen Nutzer bereits Erfahrung mit ähnlichen Systemen haben, fiel dieser Teil kompakter aus.

---

## Installationstest (manuell)

Da das Produkt als Docker-Compose-Setup ausgeliefert wird, sind keine plattformspezifischen Installationstests nötig. Die Installation wurde manuell auf einer lokalen Maschine durchgeführt. Die Docker-Container starteten korrekt und die Applikation war im Browser erreichbar und funktionsfähig. Da Docker die Laufzeitumgebung vollständig kapselt, sollte die Installation auf der Kundenmaschine identisch verlaufen.

---

## Performance Tests (manuell)

Die Performance-Analyse wurde manuell durchgeführt. Dabei wurden drei Implementierungsansätze verglichen.

### Ansätze

- **Normal (Ruby):** Ruby liest die CSV-Datei Zeile für Zeile und filtert die Ergebnisse direkt im Code
- **DuckDB:** DuckDB liest die CSV-Datei, da es dafür optimiert ist
- **DuckDB + Parquet:** Parquet ist ein spezielles Dateiformat das mit DuckDB wie eine Datenbank funktioniert und deutlich schnellere Lesezugriffe ermöglicht

---

### Lesen / Filtern (Read CSV)

> Die Gesamtzeit wird stark vom DLS-Server beeinflusst. Die `Read Avg`-Spalte zeigt den reinen Ruby/DuckDB-Overhead ohne DLS-Zeit.

| Methodik       | Zeit Max | Zeit Min | Zeit (1 Run) | Zeit Avg (6 Runs) | Read Avg |
|:---------------|:---------|:---------|:-------------|:------------------|:---------|
| **Normal**     | 14.834s  | 7.369s   | 11.961s      | 11.200s           | 4.862s   |
| **DuckDB**     | 28.868s  | 19.750s  | 28.868s      | 22.946s           | 16.753s  |
| **Parquet**    | 7.015s   | 5.275s   | 5.662s       | 6.151s            | 0.217s   |

DuckDB + Parquet ist beim reinen Lesezugriff ca. **25x schneller** als der naive Ruby-Ansatz.

---

### CSV Download

> Die Download-Funktion beinhaltet das Lesen und anschliessende Aufbereiten aller Ergebnisse als CSV-Datei.

| Methodik       | Zeit Max | Zeit Min | Zeit (1 Run) | Zeit Avg (3 Runs) |
|:---------------|:---------|:---------|:-------------|:------------------|
| **Normal**     | 181.134s | 159.552s | 159.552s     | 169.176s          |
| **DuckDB**     | 20.797s  | 9.931s   | 20.797s      | 16.072s           |
| **Parquet**    | 5.139s   | 2.464s   | 5.139s       | 4.049s            |

DuckDB + Parquet ist beim Download ca. **40x schneller** als der naive Ansatz.

---

### Memory

Nach ersten groben Memory-Messungen wurde entschieden, diesen Bereich nicht tiefer zu analysieren, da der RAM-Verbrauch insgesamt sehr gering ist.

Anmerkung: Der naive Ruby-Ansatz beansprucht so viel RAM, dass `MemProfiler` keine Messung mehr durchführen kann. Das allein zeigt, dass dieser Ansatz für den produktiven Einsatz ungeeignet ist.

|               | Parquet (10) | Parquet (50) | Parquet (200) | DuckDB (10) | DuckDB (50) | DuckDB (200) |
|:--------------|:-------------|:-------------|:--------------|:------------|:------------|:-------------|
| **allocated** | 2.79 MB      | 2.83 MB      | 2.95 MB       | 2.77 MB     | 2.81 MB     | 2.95 MB      |
| **retained**  | 0.51 MB      | 0.51 MB      | 0.51 MB       | 0.50 MB     | 0.50 MB     | 0.50 MB      |

DuckDB braucht minimal weniger Speicher als Parquet, der Unterschied ist aber vernachlässigbar. Der geringe Speichergewinn rechtfertigt den ca. 4x höheren Performance-Verlust nicht.

---

### CPU

Die CPU-Auslastung wurde manuell beobachtet, ohne konkrete Messwerte festzuhalten. DuckDB + Parquet erzeugt deutlich weniger Last als der naive Ruby-Ansatz, was konsistent mit den Zeitmessungen ist.

---

## Lasttests

Für die Lasttests wurde k6 verwendet. Das Skript testet den Filterendpunkt (`POST /api/filters`) unter steigender Last.

Konfiguration:
- Ramp-up: 30 Sekunden auf 5–100 virtuelle Nutzer
- Sustained: 1 Minute auf konstantem Level
- Ramp-down: 20 Sekunden

Schwellenwerte:
- 95. Perzentile der Antwortzeit: unter 2000 ms
- Fehlerrate: unter 1%

Die Lasttests wurden manuell durchgeführt und die definierten Schwellenwerte wurden eingehalten.

---

## Fazit

Alle 71 automatisierten Tests laufen fehlerfrei durch. Frontend-Komponenten wie React und Monaco Editor wurden manuell getestet und funktionieren wie erwartet. Die Installation via Docker wurde erfolgreich auf einer lokalen Maschine verifiziert. Die Performance-Analyse zeigt klar, dass DuckDB + Parquet die beste Lösung ist und für den produktiven Einsatz verwendet wird.
