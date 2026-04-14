# Dokumentation zu den verschiedenen Tests:

### DownloadCsvServiceTest
Ist der Test der überprüft, ob die heruntergeladene Datei tatsächlich alle Spalten herunterlädt, wie von der User-Stroy verlangt, so startet man den test:

**Container starten:**
```bash
docker compose up
```
**Umgebung auf testen einstellen:**
```bash
docker-compose exec backend bin/rails db:environment:set RAILS_ENV=test
```
**Test Datenbank synchronisieren:**
```bash
docker-compose exec backend bin/rails db:test:prepare RAILS_ENV=test
```
**Test durchführen**
```bash
docker-compose exec backend bundle exec rails test test/services/download_csv_service_test.rb
```

Wenn ein grüner Punkt steht, war der Test erfolgreich, falls ein F steht nicht.

Nach dem Test muss man zurück in den development Modus wechseln:

```bash
docker-compose exec backend bin/rails db:environment:set RAILS_ENV=development
```
Falls das nicht genügt hat, muss man noch: 

```bash
docker-compose run --rm backend rails db:schema:load db:seed
```