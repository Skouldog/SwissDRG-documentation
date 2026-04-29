# Protokoll zweites Kundenmeeting (31.3.2026)
> Sitzungsleiter: Birk, Protokollant: Carlo, Autor: Tom


## Vorestellung unserer Ergebnisse

---

- Wir stellen dem Kunden unsere erste Demo vor.(Vorführeffekt, es klappt beim ersten Versuch nicht).

- Birk improvisiert, zeigt Funktionen(Cache, Animationen, Download-Button) und erklärt grob die Herangehensweise.

- Kunde ist zufrieden mit dem Produkt, wünscht sich aber mehr Transparenz beim Programmieren (Pull Request, anstatt viele Branches).



## Fragen an den Kunden bezüglich der ersten Demo

---

- Offene fragen wurden geklärt zur jetzigen Demo.
  - 1) Welche Spalten sollen angezeigt werden -> Steht in Ticket #34
  - 2) Wie soll UI skalieren? -> Dynamisch aber mit Obergrenzen (Input Feld fest in der Breite aber nach unten erweiterbar)




## Unsere Vorschläge für die nächste Iteration

---

- Backend optimieren
- Monaco-Editor implementieren
- UI dynamisch gestalten
- Code Performance tracken
- Infinite Scrolling


## Kundenwünsche für die nächste Iteration

---
Viele Punkte haben über eingestimmt, spezifisch will der Kunde aber: 
- 1): Read-Me-Dev Datei: Dokumentation für das spätere Aufsetzten, Voraussetzungen, Libraries, etc..
- 2): Monaco Editor implementieren. 
- 3a): Ergebniss Tabelle sortieren (er empfehlt Gems anstatt manuelles Coden).
- 3b): Performance Tracking: Wie schnell gehen einzelne Prozesse und wie viel Ressourcen beanspruchen diese. 
- 4a): Pagination Gem implementieren.
- 4b): CSV-Download soll alle Zeilen zurückgeben + Optional: Den Download per Backjob abwickeln.
    
Der Kunde hat weitere zukünftige Ziele erwähnt(bsp. Patienten anklickbar machen), diese werden aber nochmals im nächsten Meeting besprochen und geplant.
Allgemein wünscht sich der Kunde für diese Iteration: Performance, den Monaco Editor und ein wenig mehr Dokumentation/Klarheit.

## Abschluss und Fragen

---
- Wir hatten diese Interation keine nennenswerten fragen.
- Nächstes Treffen mit dem Kunden wurde vereinbart(21.4). 3 Wochen Iteration wegen den Osterferien.





## Offene Punkte & Action Items

---

- Pull Request implementieren für Kundentransparenz und Code-Überprüfung.
- Erwähntes Issue vom Kunden finden und die Spaltenanzeige anpassen
- Restliche Wünsche über die kommenden Wochen implementieren.
