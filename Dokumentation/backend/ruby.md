# Intro zu Ruby

---

## Datenbanken

Die Datenbanken werden local gespeichert, wodurch sie erst initialisiert werden müssen

```ruby
docker-compose run --rm backend rails db:migrate
``` 

nun wurden die Datenbanken erstellt, haben jedoch keine Testdaten. Da unsere Daten nur lokal gespeichert 
werden muss man manuel die Daten hinzufügen. 
Erstelle den Ordner `egins` damit du am Ende den Pfad `storage/egins/` hast. 
Hier speicherst du die Egins. Zusätzlich sollte unter /storage noch der ordner "workspaces" gemacht werden, wo alles workspaces importiert werden. ~~Momentan reicht es wenn du einfach eine leere Datei Namens `TEST1.CSV` erstellst.~~
Downloade alle 3 Egins und lege sie in den Ordner.
> Wenn du die Testfile schonhattest, wäre es auch nicht schlecht die DB zu reseten um die alten Einträge und verknüpfungen zu löschen
> führe dazu den Befehl `docker-compose run --rm backend rails db:schema:load db:seed` aus. Er löscht, erstellt und führt seeds aus. Du musst danach nichts mehr machen.

Danach kannst du den Befehl 
```ruby
docker-compose run --rm backend rails db:seed
``` 
ausführen. Er ist ein Skript der unsere CSV Datei manuell mit dem Datenbank eintrag verknüpft. 
Siehe `db/migrate/seeds.rb` was genau passiert. 



## API Befehle

---

Momentan gibt es zwei Anlaufstellen 

`localhost:3001/api/egins` GET : liefert ein JSON mit allen Egins (id, title, filename) zurück.

`localhost:3001/api/filters` POST : empfängt ein JSON (workspace_id, egin_id, logik/string) und (momentan) gibt als JSON wieder zurück. Später dann weiterverarbeitung

## Test

---

Wenn ihr die DB aufgesetzt habt und die Datei erstellt/richtig benannt, dann könnt ihr den Docker starten und `localhost:3001/api/egins`
eingeben, dann solltet ihr eine Liste zurückerhalten mit den EGIN einträgen (momentan nur 1 Eintrag).

## DLS-Server

Der DLS Server ist nun im Backend implementiert. Die Anleitung wie man diesen Server installiert ist in der Docker Anleitung zu finden.
Dass der Server mit dem Backend kommunizieren kann ist im Docker-Compose geregelt. Es sind auch alle API Schnittstellen Implementiert so, 
dass das Backend dem DLS server Anfragen senden kann und der Server kann selber Anfragen zurück schicken, wenn er gewisse EGINS oder Workspaces nicht gecashed hat. 
Diese APIs wurden so implementiert wie in der Dokumentation von Lukas beschrieben (eginrepl-dsl-api.md). Die IDs die zurück geschickt werden sind in filters_controller zu finden. 

## Logs anschauen
Um die Logs des Backend anzuschauen kann man nebem dem Terminal-Tab in dem Docker läuft, einen zweites Tab öffnen und diesen CMD eingaben: `docker compose logs -f backend`. Dann sieht man die Logs, zum Beispiel auch ob der DLS Server etwas cashed.




