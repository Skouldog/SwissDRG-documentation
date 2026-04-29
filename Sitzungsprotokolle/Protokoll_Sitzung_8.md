# Protokoll 8. Sitzung (Intern, 27.4.2026)
> Sitzungsleiter: Elia, Protokollant: Tom
---


## Allgemeines & Status

- Einzelne Probleme auf lokalen Geräten behoben
- Finalen merge-commits getaged mit "v3.0" , wodurch die 4ten Iteration begonnen hat.
- Heransgehenweise abgleichen und letzte Iteration planen





---
### API Änderungen
Api vom Backend wird erweitert um Admin Konsole zu unterstützen.

```
/workspaces
-> wie bisher

/egins
-> wie bisher

/filters
-> wie bisher

/parse
    POST

/admin
    /cache-size     GET, PUT
    /threads        GET, PUT
    /workspaces     POST, DELETE    (GET nicht nötig, dies geschieht über bestehende non-admin api)
    /egins          POST, DELETE    (GET nicht nötig, dies geschieht über bestehende non-admin api)

```

### Implementierungsplan

---

Wir starten die Implementierung direkt und Arbeiten an folgenden Features:

- Frontend : Refactoring
- Frontend : Monaco editor Autocomplete
- Frontend : Admin Page Implementieren
- Frontend : Parsing implementieren
- Backend : neuer DLS Server einbinden
- Backend : Cache limit implementieren
- Backend : Parsing implementieren
- Backend : Refactoring etc.
- Backend : Admin Features bereitstellen

### Teststrategie

Zudem wird diese Interation Stress Test mithilfe von K6 implementiert und End-to-End Tests ausgeführt.


