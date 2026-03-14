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
Hier speicherst du die Egins. ~~Momentan reicht es wenn du einfach eine leere Datei Namens `TEST1.CSV` erstellst.~~
Downloade alle 3 Egins und lege sie in den Ordner

Danach kannst du den Befehl 
```ruby
docker-compose run --rm backend rails db:seed
``` 
ausführen. Er ist ein Skript der unsere CSV Datei manuell mit dem Datenbank eintrag verknüpft. 
Siehe `db/migrate/seeds.rb` was genau passiert. 



## API Befehle

---

Momentan gibt es zwei Anlaufstellen 

`localhost:3000/api/egins` GET : liefert ein JSON mit allen Egins (id, title, filename) zurück.

`localhost:3000/api/filters` POST : empfängt ein JSON (workspace_id, egin_id, logik/string) und (momentan) gibt als JSON wieder zurück. Später dann weiterverarbeitung

## Test

---

Wenn ihr die DB aufgesetzt habt und die Datei erstellt/richtig benannt, dann könnt ihr den Docker starten und `localhost:3000/api/egins`
eingeben, dann solltet ihr eine Liste zurückerhalten mit den EGIN einträgen (momentan nur 1 Eintrag).