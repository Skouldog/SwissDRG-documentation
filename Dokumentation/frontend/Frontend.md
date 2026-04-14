









Expression Input




Textarea need width 100% because default sizing not like div



inputdata in context

the getter is the actual data. the setter is a functionn however. it is needed, because then react is updating all component who use the data






Struktur der Variablen


SessionData:
    InputData:
        workspace
        egin
        logic_term
    ResultData
        elements





Damit die Typsicherheit garantiert werden kann, sind folgende Typen definiert:


Generelle Datentypen:

Egin.ts             speichert die Daten eines Egin (id, title)
Workspace.ts        speichert die Daten eines Workspaces (id, title)

Weitere Datentypen zur Verwaltung von "persistenten" Variablen

InputData.ts        fasst alle Daten zusammen, welche durch die Nutzereingabe entstehen. (Egin, Workspace, Logic_Term)
ResultData.ts       fasst alle Daten zusammen, welche durch die Auswertung entstehen. (Array)
Sessiondata.ts      fasst alle zu speichernden Daten einer Sitzung zusammen. (InputData, ResultData)