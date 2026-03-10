# Testkonzept v1

## <u>Unit Tests </u>


* ### <u> Interpretation der Logiksprache </u>
KEINE Tests, da das zuständige Backend bereit gestellt wird.

* ### <u> Monaco-Editor </u>
Da es sich um ein etabliertes OpenSource Projekt handelt wird die allgemeine Funktionsweise nicht getestet. Unsere eigene Konfiguration wird zu einem späteren Zeitpunkt, nachdem der Editor implemtiert wurde, getestet. Dies geschieht durch eine Art Fuzzing, bei welchem die korrekten Ausdrücke zufällig verfälscht werden und dann getestet wird, ob sie korrekt als Fehlerhaft markiert werden

* ### <u> Title </u>

* ### <u> Title </u>




## <u> Datenbank Tests </u>
Da die Egin von SwissDRG stammt führen wir keine Datenbank Tests durch.

## <u> Integrationstest </u>
 Wir schreiben Manuelle Tests, bei welchen Logikausdrücke Eingegeben werden und geschaut wird, ob die Korrekten Informationen angezeigt werden.

## <u> GUI Test </u>

Da es sich um ein eine simple UI handelt, wird diese "von Hand" getestet.

## <u>Stress-Test</u>
Zur Zeit gibt es noch keine spezifischen Anforderungen bezüglich der belastbarkeit. Dies zu definieren wird Teil eines zukünftigen Kundenmeetings, bei welchem auch spezifiziert wird, wie diese Tests abzuhalten sind.

## <u> Usability-Test </u>
Da es sich um eine kleine, simple UI handelt und die zukünftigen User bereits realtiv viele Erfahrung mit ähnlichen Systeme haben fällt dieses Testen nicht all zu gross aus. Um bezüglich Usability "auf sicher" zu gehen, planen wir ein paar wenige Cognitive Walkthroughs mit nicht-technischen User.