# Protokoll 7. Sitzung (4.& letztes Kundenmeeting, 12.5.2026)
> Sitzungsleiter: Raphael, Protokollant: Elia



Demo der Iteration 4
---
Der Kunde war sehr zufrieden mit der Demo der 4. Iteration, welche bis auf ein paar wenige Elemente auch das Endprodukt darstellt.
Neu für die letzte Iteration war:
- **implementation der Admin Page als seperater Komponent**
   - **Egins & WS**
     
     Alle aktuellen Egins & Workspaces werden untereinnander Aufgelistet. Man kann die einzelnen Dateien löschen oder unterhalb der Auflistung eine neue Datei hochladen. Beides geschieht über Pop-ups. 
        - *Egins/ Workspaces hochladen*
    
          Beim Hochladen einer neuen Datei wird der Titel von der Datei übernommen, was bei ihnen auch bissher der Fall ist, also stimmt das genau so. Das Hochladen ist aktuell ein synchroner Prozess, i.e. in dieser Zeit nicht für andere Request ansprechbar. Dies ist kein Problem, da man ja auch nicht gleichzeitig mehrere Einstellungen ändert, sondern eine nach der anderen. 
    


   - **Cache Size**
 
     Unterhalb der Egin/WS Übersicht kann man die grösse der Caches für die Anfragen und Downloads individuell anpassen. Ist die Cachegrösse erreicht, so wird jeweils der Eintrag gelöscht, dessen Nutzung am weitesten zurück liegt. 

   - **max # Threads**
 
     Zuunterst auf der admin Page kann man auch die maximale Anzahl der gebrauchten Threads. Es braucht immer minimum einen Puma Thread und einen Goodjob Thread (i.e. mindestens 2 Threads). Anders als bei den anderen Einstellung muss der Server neu gestartet werden, dass diese Änderungen effektiv sind. Man könnte implementieren dass dies automatisch oder via einen Button geschieht, da das Projekt jedoch praktisch fertig ist reicht der Hinweis innerhalb des Pop-ups.

  
- **Bestätigungs Pop-ups**
  
  Bei jeder Einstellungsänderung (sei dies hochladen, löschen oder Wert verändern) kommt ein Pop Up, in welchem der Nutzer der Nutzer seine Änderung eingeben kann und sie dann bestätigen muss, damit sie gespeichert werden. Sobald die Änderung vom Backend bestätigt wurde wird die Seit automatisch geupdated. Diese Pop-ups entspringen des Pop-ups um auf eine spezifische Seite zu springen, wurden jedoch refactored, sodass nun die Admin und "normale" Page zugriff auf die Funktionalität haben. Aktuell dauert dass Hochladen von Egins/ WS realtiv lange, ohne dass visuell kommuniziert wird dass etwas im Hintergrund passiert. Deshalb ist eines der allerletzten Features, welches noch zu implementieren ist eine Ladeanimation. Im Frontend kann man via min & max Werte den Wertebereich von Änderungen einschränken. Somit kann garantiert werden, dass es im Backend keine Fehler bezüglich invaliden Einstellungen entstehen. 
  
- **Parsing**

  Nach 200ms ohne veränderung des Input-Feldes wird der Logikausdruck an ihr Backend geschickt, um zu prüfen ob er valide ist. Da zurückkommenden Fehlermeldungen teilweise sehr lange sind, wird lediglich die erste Zeile davon in einem neuen Feld zwischen Logikausdruck und "Search-Button" angezeigt. Ein mögliches nächsten Feature wäre, dass man diese Zeile expandieren könnte, jedoch ist dies klar optional und wird wahrscheinllich bei verlangen von SwissDrg implementiert. Der Such Button wird nun auch erst klickbar, nachdem der Logikausdruck als valide geparsed wurde, dasselbe gilt für den Download-Button.   

Patientenverlinkung
---
Da die PatientenIds von den Egins/WS abhängen und nicht unversell sind und die deren Ids durch löschen & erneutes Hochladen in unserem Backend sich ändern, konnten wir die Patientenverlinkung bisher nicht nach ihrem Schema implementieren. Wir erhielten nur die einzelnen Egins und Workspaces und haben keinen Zugriff auf deren Ids in ihrem Backend, wesshalb er meinte, sie könnten es implementieren.

Testing
---
Unsere Backend Tests verfügen nicht über eine 100% line Coverage, jedoch testen wir die wichtigsten Klassen, ob die Ids übereinstimmen und vor allem von Hand. Der DLS Server eine Black Boc für uns sind, weshalb wir für entsprechende Tests jeweils Mock-objekte verwenden. Da im Frontend ausführliche Tests sehr aufwendig sind, reichen die aktuellen funktionalitätstest von Hand. 

Projektabgabe
---
Wir übergeben ihnen das Repo, wir sollten aber auch ein DockerImage als tar file bei NextCkoud hochladen, wofür er uns nach dem Gespräch die nötigen Befugnisse gab. Im Readme sollten wir noch die 1-2 Befehle für die initiierung hinterlegen. Die Idee ist, dass wir zwiei Docker Images hochladen: ein Development und in Production, wobei die Umgebungsvariabeln als ENV übergeben werden.

Allgemeines Feedback
---
Der Kunde scheint allgemein mit dem Projekt sehr zufrieden. "100% der Funktionalität" hätten wir erreicht. Die Ladeanimation beim Hochladen von Dateien reichte zeitlich knapp nicht mehr für diese Iteration, wollten wir aber sowieso noch implementieren. Ansonst gäbe es noch weitere Features, welche theoretisch noch cool wären, jedoch sind diese nicht sehr wichtig für die fertigstellung des Projektes und könnten bei gebrauch noch von ihnen Implementiert werden. Zu diesen features gehört zum Beispiel die Möglichkeit die Anzahl Resultate pro Seite zu verändern. Falls wir noch weitere Ideen für Features haben, können wir diese in einem Markdown in der Dokumentation hinterlegen.  
Sehr wichtig für den Projektabschluss ist hingegen die (stichwortartige) Dokumentation inklusive Diagramme, sodass sie weiter an unserem Code arbeiten können. 
Bezüglich Deadline gilt mehr oder weniger das Ende des Semesters, da wir an einem Ende des Projektes interessiert sind, wird es wohl aber bereits früher fertig sein.

Abschluss
---
Gegen Ende diskutierten wir noch den Gebrauch von KI bei diesem Projekt, sowie ihre verwendung von KI / Agenten. Ganz am Ende informierte er uns auch, dass sie zurzeit eine freie Stelle von ca. 50% haben.

Insgesammt scheint er sehr zufrieden mit unserer Arbeit!

