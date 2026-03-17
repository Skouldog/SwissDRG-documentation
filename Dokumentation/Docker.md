# Docker Anleitung

## 1. Start
Bevor man mit dem Coden beginnt immer schauen, dass man in git im richtigen branch ist un auf dem korrekten Stand. Danach den container starten: 

**docker-compose up bzw mit docker-compose up --build**

Logs können in der Docker Desktopanwendung angeschaut werden. 

## 2. Wo wird programmiert?
Man arbeitet ganz normal in Rubymine in den lokalen Ordnern auf dem Rechner. Docker spiegelt deine Änderungen sofort in die Container:

Frontend: Änderungen in frontend werden dank Vite sofort im Browser unter [localhost:3001] sichtbar.

Backend: Änderungen in backend werden von Rails sofort übernommen: [localhost:3000].

## 3. Wichtige Spezial-Befehle
Da alles in isolierten containern läuft, muss man Befehle, die Rails oder npm betreffen, über Docker anstossen. Dabei kann man das --rm Flag nutzen, damit keine "Container-Leichen" zurückbleiben.

### A. Datenbank-Änderungen (für rails wichtig)
Wenn jemand eine neue Tabelle erstellt hat (Migration), musst man die lokale Datenbank aktualisieren:

**docker-compose run --rm backend rails db:migrate**

### B. Neue Libraries hinzufügen
nicht lokal auf dem Laptop installieren!

Frontend: **docker-compose run --rm frontend npm install [paket-name]**

Backend: **docker-compose run --rm backend bundle add [gem-name]**

Danach für alle: Einmal **docker-compose up --build** ausführen, damit das Image aktualisiert wird.

### C. Rails Generatoren (Controller/Models)

**docker-compose run --rm backend rails generate controller Patients**

oder ein Beispiel für models: 

**docker-compose run --rm backend rails generate model Patient first_name:string last_name:string birth_date:date insurance_number:string**

### 4. Troubleshooting 

Falls die Website nicht lädt oder Docker "spinnt", hilft meistens ein kompletter Neustart:

Stoppen & Aufräumen: **docker-compose down** (oder STRG+C/control+C)

Neu bauen (ohne Cache): **docker-compose build --no-cache**

Starten: **docker-compose up**

Datenbank-Reset (Notfall): **docker-compose run --rm backend rails db:prepare**

## 5. Container wieder beenden

Container können mir **STRG+C oder control+C** sowie mit **docker-compose stop** wieder beendet werden. 

