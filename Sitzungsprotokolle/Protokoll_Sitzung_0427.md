# Kurze interne Besprechung 27.04.26

---


Als Erstes wurden kurz einige kleien Probleme auf einzelenen Geräten der Gruppe behoben. Im anschluss wurden die 
finalen merge-commits getaged mit "v3.0" sodass mit der Implementationsphase der 4ten Iteration begonnen werden kann.

Dazu haben wir die individuell vorbereiteten Herangehensweisen kurz verglichen und besprochen.
Dabei wurden folgende Dinge bestimmt:


### API

---

Die Api die das Backend zur Verfügung stellt, wird erweitert um den Anforderungen der 4ten Iteration gerecht zu werden.

Die API Schnittstelle sieht ab jetzt wie folgt aus:

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

\+ Teststrategie



