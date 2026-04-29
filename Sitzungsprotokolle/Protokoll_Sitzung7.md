# Protokoll 7. Sitzung (3. Kundenmeeting, 21.4.2026)
> Sitzungsleiter: Birk, Protokollant: Elia
> Letzte Woche gab es kein Meeting (14.4) -> Kein Protokoll


Demo der Iteration 3
---
Die Demo Kam sehr gut an, unter den neuen Features zählten 
-  verbesserte Pagination
-  die Fenstergrösse dynamisch anpassbar
-Sortieren der Spalten (ggf. nach String oder int)
- der Download läuft nun als Background Job und ist so kein Bottleneck mehr
- Patientenverlinkung durch eine URL basierend auf der PatientenId (BasisURL + PatientenId)
- Monaco Editor färbt während der Logikeingabe Wörter aus dem Lexer Vorgabensgemäss ein
- schnelleres Backend durch die konvertierung der CSV  zu Parquet Files, welche mithilfe DuckDB gespeichert werden 

Während der Demop trat auch ein Bug auf: nach einer Abfrage wird das Resultat für den Download gecached. Passiert eine neue Abfrage und während diese lädt wird der Download gepresst, so wird die zuletzt gechachte File heruntergeladen und nicht die aktuelle Abfrage.

Wir verbrachten einige Zeit damit, ihm anhand der logs zu zeigen, dass ihr Backend einen Grossteil der Zeit für eine Abrafe in Anspruch nimmt.


Unsere  Fragen
---
- Aktuell besteht die Patientenverlinkung aus einer Patientenspezifischen URL, wie genau sie jedoch aussehen soll wird er uns noch schicken.
- Bezüglich des Monaco Editors schickt er uns noch die Klarifikation bezüglich welche Tokens als BOOLEAN_OPERATOR und CODE_VARIABLE zählen, sowie die API Dokumentation, sodass wir den Logikausdruck an den Server schicken können und dieser Antwortet, ob der Ausdruck korrekt ist oder nicht.
- Für das Delivery reicht das repo mit ausführlicher Dokumentation, einem Inbetriebnahme.md und eine Stichwortartige Erklärung wie die Prozesse genau ablaufen.
- Wir sollten eine Art Admin/Settings page einführen (keihne Authentifizierung nötig), welche das Löschen /Hinzufügen von WS/Egins erlaubt, sowie variabeln bestimmen für die Anzahl der gebrauchten Threads und gespeicherten Caches bevor alte gelöscht werden (ebenso für Download).
- Da maximal 5 Personen gleichzeitig die Komponente brauchen werden, reicht die aktuelle Performancee, auch die UI muss nicht getestet werden.
- Es gab noch zwei Klarifikationen bezüglich welche Spalten angezeigt werden sollten: Gesammtkosten sind in den Egins als "ctaze" beschriftet und anstelle von drg ist drg_bisher gemeint (die Namen aus den Egins sind zu übernehmen).



---
Nächste Iteration
---
### Bugfixes
Zurzeit sind 2 Bugs bekannt: bezüglich der Pagination kommt man auf die letzte Seite +1 und während einer Abfrage sollte der Download Button ausgegraut werden. 
### - Monaco-Editor
Mithilfe der API sollte der Logikausdruck auf seine Korrektheit geprüft werden und dessen Rückmeldung in den Monaco-Editor eingebettet werden. Gegebenfalls braucht es hierfür jeweils einen Delay oder eine Queue. 


### Refactor & Dokumentation
Hier liegt der Fokus vor allem auf der Dokumentation: sehr ausführlich, inklusive einem Inbetriebnahme.md, so dass die Aufsetzung auf ihrer Seite Problemlos funktioniert. 

### Settings/Admin und kleine UI improvemnts
Wie genau dies zu implementieren ist, wird uns überlassen, jedoch sollte es das Löschen & Hinzufügen (inkl. db:migrate & db:seed) von Egins/Workspaces ermöglichen. Auch sollte es erlauben Variabeln bezüglich der # Caches, # Threads und Seitengrösse (10,20,50,100) anzupassen. Authentifizierung braucht es nicht.
Es kamen auch ein paar kleine UI changes auf: nur Search (anstelle von Search for Matches) und die # Resultat-Anzeige und Zeit sollten etwas grösser sein, wobei letztere genauer Unterteilt werden soll. Diese Änderungen sind jedoch nicht sehr wichtig...


### Backend Testing
Im Frontend breaucht es keine Usability Tests, falls wir unbedingt möchten können wir diese aber durchführen. Im Backend braucht es jedoch noch Tests, da bisher Tests nicht sehr im Vordergrund standen

---
### Das nächste Kundenmeeting findet in 3 Wochen, am 12.5 um 10:15 statt.
