# Protokoll vierte Sitzung (Kundenmeeting, 18.3.2026)
> Sitzungsleiter: Birk, Protokollant: Elia
> 
> Auch wenn Meeting sehr nach Plan verlief, ist dieses Protokoll viel eher nach Thematik als Chronologie gegliedert und sollte viel eher dazu helfen eine Übersicht über die wichtigen Informationen zu geben und nicht per se der Fluss des Gesprächs wiederspiegeln
---

Demo der ersten Iteration (Prototyp)
---
Die Demo kam sehr gut an ("Sieht sehr gut aus von aussen") und Lukas schien sehr zufrieden. Vor allem das Farbschema welches wir wählten wird auch schon von ihnen gebraucht.

---
Unsere  Fragen
---
### Sessionmodell mit teilbaren Links:

Dass die Abfragen via Links teilbar sind ist ein erwünschtes Feature, jedoch steht es nicht zuoberst auf der Prioritätsliste. Wir erklärten unsere Idee, die Abfragen an der DRS über SessionIds zu cachen, jedoch schien ihn das nicht sehr zu überzeugen. 
Er sagt, er möchte viel eher, dass die Egin, Workspace und Logikausdruck als 3 Schlüssel fungieren und diese dann geteilt werden können. Ob wir den Logikausdruck einfach als Hash oder als String oder wie auch immer implementieren möchten überlässt er uns.

### Workspaces (und Egins)
- Bei den Workspaces handelt es sich um eine Zip-Datei mit einer komplizierten Ordnerstruktur. Wir müssen diese aber nie öffnen, denn sie wird vom DRS gecached.
- Die workspaces schickt er uns noch und es gibt ca. 25 Workspaces.
- Auch wenn sie keine Personendaten enthalten, dürfen Workspaces NICHT publiziert werden, also sie werden genau wie die Egins gehandhabt.
- Egins sind teilweise bis zu 90% ähnlich und sehen von Jahr zu Jahr komplett anders aus. 




## Optimierungen
Auch wenn der Fokus zur Zeit vor allem darin besteht, alles "zum laufen zu bringen", hatten wir bereits einige Ideen um den gesammten Prozess zu optimieren.
Auf die Frage, was die Geschwindigkeitsanforderungen seien, sagte er 3 Sekunden wäre ideal, jedoch kann es sein dass die Abfrage an den DLS bereits länger als das dauert. 

<div style="display:flex; gap:24px; align-items:flex-start;"> <div style="flex:1; min-width:300px;">

### Precaching
Wir haben ihm vorgestellt, wie wir die Abfragen an den DRS optimieren möchten:
Anstatt den DRS abzufragen, nachdem Egin, Workspace und Logikausdruck eigegeben wurde, könnte man es in 2 Abrfagen unterteilen:

Wir gehen davon aus, dass jeweils zuerst die Egin und Worspace ausgewählt  und erst danach der Logikausdruck Eigegeben wird. Da man gegebenenfalls den gesammte Egin /Workspace schicken muss (was relativ lange dauert), erschien es uns sinnvoll den Request ohne Logikausdruck zu schicken, sodass im Hintergrund bereits geladen werden kann, während der Nutzer den Ausdruck noch ein gibt.

Zur Zeit funktioniert ihr DLS aber nur mit requests, welche auch einen Logikausdruck besitzen. Er schaue noch, ob sie dies noch abändern, jedoch ist diese Optimierung zur Zeit von geringer Priorität.

</div><div style="flex:1; min-width:300px;">

<pre>
Grafik zur Kommunikation DLS-BAckend
(ws: workspace)
 Backend         DLS
    |              |
    |--eID,wID,exp→|
    |←----eID------|
    |-----egin----→|
    |←----wID------|
    |------ws-----→|
    |←-------------|
</pre></div></div>

### Ids in Datenbank Cachen/ Dashboard
Wir haben auch vorgeschlagen, die Ids von den Egins bzw. workspaces in einer Datenbank zu cachen, sodass es später geht. Da die Egins/workspaces sowieso lokal und nicht auf GitHub gespeichert sind, spielt es keine Rolle ob wir eine Datenbank verwenden oder nicht. Er hat uns aber stark angeraten, vor der Implementation zu schauen, ob es überhaupt eine Datenbank braucht oder ob es bereits schnell genug ist.
Um in Zukunft das erweitern um Egins und workspaces zu vereinfachen schlugen wir auch vor ein Dashbord zu implementieren, welche einen Überblick über alle Workspaces / Egins gibt und auch das hinzufügen/ löschen ermöglicht. Dieser Vorschlag kam sehr gut an, wird aber sicherlich erst in eine spätere Iteration einfliessen.








---
Nächste Iteration
---
Die 2. Iteration besteht vor allem aus 2 Teilen:
- ### Integration DLS
Er schickt uns bis Ende Woche das Image des Dockers des DLS, die Arbeitsbereiche, sowie die API Dokumentation.  Das Hauptstück dieser Iteration ist es diese zu implementieren und die Kommunikation zwischen dem DLS und unserem Backend zum laufen zu bringen.


- ### Resultatansicht
Auch hier schickt er uns noch eine Liste mit welche Spalten angezeigt werden sollten, jedoch sollte es sicher in Form einer Tabelle sein. Ein wichtiges Element was wir auch noch implementieren müssen ist, dass es Anzeigt, wie viele Resultate es gibt. 

Das Frontend sollte auch "dehnbar" sein, als dass man die grösse des Fensters dynamisch ändern kann. 


### Optionale Features / für spätere Iterationen

- Es wäre noch cool, wenn es ein Tool gäbe, welches anzeigt wie lange jeweils die einzelnen Schritte dauern (DLS vs. Resultat bearbeitung), was uns dann überhaupt ermöglicht zu sehen wie viel besser die (eventuellen) Optimierungen sind, geschweige denn ob sie überhaupt nötig sind.

- Ob wir ein ∞-scrolling oder Paging implementieren überlässt er uns, jedoch hat er uns darauf hingewiesen, dass scrolling halt "moderner wirkt" und in der Realität durchpagen relativ selten ist.

- Es sollte auch möglich sein, dass wenn man auf einen Patient drückt, dies zum Patienten verlinkt ist. Da wir aber nicht in ihrem System drin sind, ist noch nicht 100% klar, wie diese verlinkung aussehen soll. 

- Sehr wahrscheinlich erst in einer späteren Iteration sollte es möglich sein das Resultat (alle Resultate sowie Spalten) als CSV herunterzuladen. Hierfür gibt es jedoch bereits Libraries bzw. gems.

- Bezüglich des Monaco-Editors wird uns noch eine Lexer-Datei schicken, welche alle Begriffe enthält, die zu Highlighten sind. Diese Liste ist sehr statisch, also wird voraussichtlich nie geändert.

### Das nächste Kundenmeeting findet in 2 Wochen, am 31.3 um 10:15 statt.
