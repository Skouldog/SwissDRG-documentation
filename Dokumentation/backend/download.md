# Dokumentation: Backgroundjob CSV-Download (Worker)

## Zusammenfassung
Bisher hat das Generieren von grossen CSV-Dateien den Haupt-Webserver **backend** blockiert. Das führte dazu, dass das Frontend beim Blättern durch die Suchergebnisse ("Next Page") oder bei parallelen Anfragen eingefroren ist, bis die CSV fertig gerechnet war.

**Die Lösung:** Wir haben die CSV-Generierung in einen separaten **Background-Worker** ausgelagert. Der Webserver antwortet jetzt sofort auf Frontend-Anfragen, während der Worker die Datei parallel im Hintergrund baut.

---

## Was wurde technisch geändert?

1. **Neuer Docker-Container (worker):** In der Architektur läuft nun neben dem Backend ein Prozess, der ausschliesslich für Hintergrundaufgaben zuständig ist.
2. **GoodJob Integration:** Wir nutzen das Gem good_job und unsere bestehende Postgres-Datenbank, um die Warteschlange für die Jobs zu verwalten.
3. **Schutz vor Doppelberechnung:** Wenn ein User schnell klickt oder durch Seiten blättert, prüft die Datenbank (via `query_hash` und der Spalte `generating_csv`), ob die Datei für diese spezifische Suche bereits gebaut wird. Redundante Jobs sollten blockiert werden.

---

## Wie funktioniert der Ablauf jetzt?

1. **Suche:** Der User führt eine Suche im Frontend aus.
2. **Job-Zuweisung:** Das Backend speichert die Suchparameter, markiert den Download als "in Arbeit" (`generating_csv = true`) und übergibt den Auftrag an den Worker.
3. **Hintergrund-Arbeit:** Der worker-Container generiert die CSV und legt sie in ActiveStorage ab.
4. **Download:** Klickt der User auf "Download":
    * **Ist die Datei fertig:** Sie wird direkt ausgeliefert.
    * **Ist die Datei noch in Arbeit:** Der Endpunkt wartet im Hintergrund, bis der Worker fertig ist, und schickt die Datei dann ab.

---

## Nutzung (Frontend / API)

**Für das Frontend und die API-Nutzung ändert sich nichts** Die Endpunkte für die Suche (`POST /api/filters`) und den Download (`GET /api/filters/download`) bleiben gleich.

---

## Setup

neues Gem & neuer Container also neu builden und die Datenbank migrieren:

```bash
docker-compose down
docker-compose up -d --build
docker-compose exec backend bin/rails db:migrate