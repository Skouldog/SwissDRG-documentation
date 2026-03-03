## Protokoll zur ersten Sitzung 

### Kontext: 
SwissDRG rechnet ab, wie viel Geld ein Spital für bestimmte Leistungen verdient (DRG System = pauschalisierendes Abrechnungssystem)

### Protokoll:
Zuerst hat sich Lukas vorgestellt und gerade allgemin kurz erklärt was wir grob machen:  

SwissDRG muss viele Fallabfragen durchführen. Bisher geschieht die Suche nach Patienten über Dropdown-Menüs.  
Wir sollen nun ein tool entwickeln, dass unter Verwendung ihrer eigenen Logiksprache diese Suche vereinfacht.  
Dazu bieten sie uns den sogenanten DLS (langugae server) der die Logiksprache die eingetippt wird interpretiert und uns IDs liefert.
Die Idee ist, dass unser Frontend den Logik Ausdruck vom User entgegennimmt und die Eingabe dieses Ausdrucks durch Verwendung des Monaco Editors  
vereinfacht wird. Dieser Ausdruck wird dann von der React Komponente an unser Backend geschickt. Das Backend   
handhabt dann die Interaktion mit dem DLS und dem CSV file (der Egin). Der DLS liefert Patienten IDs und das Backend holt die entsprechenden  
Informationen aus dem CSV, diese werden dann an das Frontend übergeben und angezeigt.  

Danach haben wir uns vorgestellt und unsere Rollen im Projekt erklärt. 

Danach haben wir geklärt, dass Carlo (Key Account Manager) mit Lukas via Mail kommuniziert. Weiter werden wir Lukas Zugriff auf unser  
GitHub Repo mit allen Dokumenatationen geben, und er kann dort in unsrer Statusberichte usw. schauen. Wir haben ihm erklärt, dass wir  
uns als Team einmal pro Woche treffen um das Projekt zu besprechen und alle Berichte dazu auch auf dem Repo zu finden sind.  
Auf dieses Github werden wir auch die User stories hochladen.
Wir haben mit Lukas auch abgemacht, dass wir uns alle 2 Wochen mit ihm treffen (das sind unsrere Iterationen). 
  
Danach haben wir das Planning Game durchgeführt:  
  
Lukas hat gesagt wir sollten mit dem Frontend anfangen. Da er uns noch genaue Anleitungen liefern wird zu der Verwendung der DLS  
und es sonst noch Sachen gibt die Abgeklärt werden müssen (Datenschutz sowie technologische Entscheidungen von ihrer Seite)  
Das Frontend soll in der ersten Iteration als React Komponente entwickelt werden und ein einfaches Textfeld enthalten  
Dieses Textfeld wird einmal mit dem Monaco Editor erweitert und dient der Eingabe der Logikausdrücke. Die Eingaben die  
dort getätigt werden sollten, dann schonmal ans Backend gesendet werden können um diese Kommunikation schonmal grundlegend  
aufzubauen und ein wenig zu testen. Weiter soll das Frontend zwei Dropdowns enthalten eines um den Arbeitsbereich auszuwählen  
(Diesen kann man sich als Kontext für den DLS vorstellen und muss bei DLS Abfragen mitgesendet werden) und das zweite Dropdown   
ist für die EGIN (also welche Liste durchsucht werden soll, ebenfalls Kontext für die DLS Abfragen). Diese EGIN bekommen wir noch nicht,  
da stellen sich noch Datenschutzfragen, wir sollen einfach ein Dummy CSV file machen un dieses verwenden. Die EGIN soll  
ohne Eingabe bereits in der UI angezeigt werden und dann bei einer Abfrage sollten nur noch die Teile angezeigt werden die spezifiziert wurden.  
Die EGIN ist einfach ein CSV file. In späteren Iterationen werden dann der Monaco Editor sowie das ganze Backend implementiert.  
  
Zusammenfassung: Im ersten Sprint werden wir ein Frontend dummy bauen, basierend auf dem werden wir das programm dann erweitern.  
Diese UI sollte relativ rudimentär gehalten sein. 

Danach haben wir noch über die Umsetzung des Projekts gesprochen und welche Technologien wir verwenden werde und was sie uns liefern:  

Lukas hat uns grob erklärt wie die Interaktion vom REPL mit dem DLS funktioniert und wird uns dann eine genaue "Anleitung"  
noch zur Verfügung stellen. Weiter hat er uns darauf hingewiesen dass die EGIN sowie die Arbeitsbereiche nicht auf GitHub landen  
dürfen --> Gitignore und ein privates Repo machen für unsere Software die wir entwickeln. Er wird uns auch noch  
spezifizieren wie wir wissen welche Spalen aus der EGIN angezeigt werden sollen (sind insgesamt ca. 200). Er liefert uns  
auch eine Liste mit allen Wörtern nach denen wir im Monaco editor filtern sollen (also highlighten und Autofillen usw.)  
den DLS wird er uns in einem Dockerfile geben und wir werden Anfragen an diesen via HTTPS Requests machen. Er hat auch gemeint  
Dass die Idee ist, dass das Frontend nur für die Eingabe des Logikausdrucks ist und zum Anzeigen der Resultate, alles andere übernimmt  
Ruby on Rails backend. Weiter hat er gemeint, dass es okay ist wennd as Projekt lokal bei uns auf den Rechnern funktioniert.  
Wir haben noch geklärt das wir Issues in guthub brauchen werden und diese mit Kommentaren versehen werden um klar zu machen wo wir so stehen.  

### Nächstes Meeting mit Lukas ist am 17.03 um 15.15
