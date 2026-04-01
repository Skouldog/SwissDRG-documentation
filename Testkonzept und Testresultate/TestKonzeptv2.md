# Testkonzept v2

## <u>Unit Tests </u>


* ### <u> Interpretation der Logiksprache </u>
//KEINE Tests, da das zuständige Backend bereit gestellt wird.

* ### <u> Monaco-Editor </u>
Noch keine Test, da dies erst in der kommenden Iteration implementiert wird

* ### <u> React Frontend </u>
//Beim frontend geht es zunächst vor allem um die visuelle Darstellung der Elemente, wofür manuelle visuelle Tests reichen. 

* ### <u> Ruby Backend </u>
Wir testen unsere Controller die vom Frontend abgefragt werde: 
- /api/egins und /api/workspaces : hier reicht bereits eine simple Http-Anfrage um zu sehen ob wir die gewünschten Einträge erhalten/die DB ausgelesen wird.
- /filters : Dies wird momentan noch manuell beurteilt. Wir schicken eine Anfrage an den DLS-Server und erhalten eine Antwort(Erwarten korrekte Ausführung, da dies vom Kunden bereit gestellt wird). Der Array mit ID's wird dann verwendet um Patienten in der CSV zu finden. Momenta manuell: Vergleiche die ersten 10 und letzten 10 Einträge des DLS-Arrays mit unser erzeugten Tabelle und Vergleiche Array.size mit Anzahl Ergebnisse
 




## <u> Datenbank Tests </u>
//Da die Egin von SwissDRG stammt führen wir keine Datenbank Tests durch.

## <u> Integrationstest </u>
 //Wir schreiben Manuelle Tests, bei welchen Logikausdrücke Eingegeben werden und geschaut wird, ob die Korrekten Informationen angezeigt werden. 
Wir definieren hierbei unterschiedliche Szenarien.
- Optimal Szenario: User gibt gültige Anfrage ein und sollte gültige Antwort erhalten.
- Fehler Szenario: user gibt ungültige Anfrage ein und sollte eine Fehlernachricht erhalten. ( Stand 31.3, ist dies in der Console ein Fehler, aber nicht sichtbar im GUI).

## <u> Installationstest </u>
//Da das Endprodukt eine React-Komponente ist und wir mit Docker arbeiten, braucht es keine plattformspezifischen Installationstests. Es reicht zu prüfen, ob die Docker-Container korrekt starten und die Applikation im Browser erreichbar und funktionsfähig ist.


## <u> GUI Test </u>

//Da es sich um ein eine simple UI handelt, wird diese "von Hand" getestet.

## <u>Stress-Test</u>
//Zur Zeit gibt es noch keine spezifischen Anforderungen bezüglich der belastbarkeit. Dies zu definieren wird Teil eines zukünftigen Kundenmeetings, bei welchem auch spezifiziert wird, wie diese Tests abzuhalten sind.

## <u> Usability-Test </u>
//Da es sich um eine kleine, simple UI handelt und die zukünftigen User bereits realtiv viele Erfahrung mit ähnlichen Systeme haben fällt dieses Testen nicht all zu gross aus. Um bezüglich Usability "auf sicher" zu gehen, planen wir ein paar wenige Cognitive Walkthroughs mit nicht-technischen User.


## Anmerkung
Da die gesamte Anwendung recht simpel ist und es hauptsächlich darum geht eine Tabelle anzuzeigen, sind die Ausführbarentest ziemlich limitert. Mit einfachen for Loop sind die Ergebnisse bereits richtig. Der stärkere Fokus liegt in der Optimierung, wofür bis jetzt nur Konsolen-Logs benutzen, aber in der nächsten Iteration spezifische Ruby Gems verwenden werden. Diese sollen Zeit und verbrauchte Ressourcen messen. Diese Perfomance-Test sind weit aus interessanter für uns und unseren Kunden, um die Anwendung zu beurteilen.
