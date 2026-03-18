# Protokoll dritte Sitzung (intern, 17.3.2026)
> Sitzungsleiter: Carlo, Protokollant: Elia


## Allgemeines & Status

---

- kein Happy Birthday Birk! ;)

- Alle auf aktuellen Projektstand gebracht: Bei allen funktioniert der Prototyp (1. Iteration)



## Technischer Wissensaustausch

---
- Frontend: Birk hat das Komponentendiagramm sowie den React code gezeigt und in Detail erklärt, sowie die genaue Funktionsweise der Kommunikation zwischen Front- und Backend via JSON Dateien erläutert.

- Backend: Tom erklärte wie das Backend bisher funktioniert 

- Wir diskutierten, wie wir am besten und effizientesten mit den Egins und den Patienten Ids (vom DRS) umgehen sollen. Wir einigten uns darauf, dass es am sinnvollsten ist, die Liste der Patienten Ids als Session inkl. Id zu Speichern, sodass das Frontend nur noch die SessionId und die Anzahl der Gewünschten Patienten ans Backend schicken muss und falls mehr Patienten angezeigt werden sollen, nicht erneut der DRS angesprochen werden muss.

- Ein Weiterer Punkt war die Frage, ob wir beim Result Panel ein infinite Scrolling oder lieber Seitenweises Anzeigen implementieren möchten. Man könnte so auch ermöglichen, dass man bei einer Suche den Link teilen kann und dieser genau dieselbe Patienten zeigen. Dies ist jedoch etwas, wo wir den Kunden Fragen müssen, was er genau haben möchte.

- Es wurde noch kurz das Testkonzeptv1 angeschaut und da es zur Zeit noch nicht gross etwas zu Testen gibt, wurde sich darauf geinigt, Tests in eine spätere Iteration einzubinden.




## Setup & Problembehebung

---
- Carlo hatte noch Probleme, weshalb der Protoyp auf seinem Rechner nicht funktionierte. Nach einem löschen und erneut herunterladen des gesammten Projektes schienen diese Fehler jedoch behoben.




## Aufgabenverteilung (Sprint / Arbeitsplan)

---

- Da später am selben Tag das 2. Meeting mit dem Kunden ansteht und erst dann die 2. Iteration fixiert wird, konnten wir noch nicht definieren, was überhaupt getan werden muss. Es wurde sich jedoch darauf geeinigt, dass Tom und Carlo sich auf das Rails Backend und Elia und Birk auf das React Frontend fokussieren.

## Offene Punkte & Action Items

---

- Es wurden noch die Punkte des groben Sitzungsplan für das Kundenmeeting finalisiert und an Swiss DRG geschickt.


