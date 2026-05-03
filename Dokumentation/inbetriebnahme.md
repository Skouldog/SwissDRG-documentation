# Dokumentation zur Inbetriebnahme
Diese Anleitung beschreibt die notwendigen Schritte, um die Entwicklungsumgebung und das Tool mithilfe von Docker in Betrieb zu nehmen.

## 1. Voraussetzungen
Docker & Docker Desktop: Stellen Sie sicher, dass Docker auf Ihrem System installiert ist und ausgeführt wird.

Git-Stand: Bevor Sie beginnen, stellen Sie sicher, dass Sie sich im richtigen Branch befinden und den aktuellen Stand des Repositories geladen haben.

DLS Server Datei: Laden Sie die Datei **drg-language-server-1-3-1.tar** von der SwissDRG Cloud herunter.

## 2. Vorbereitung der Container-Images
DLS Server implementieren:
Der DLS Server muss als Docker-Image geladen werden. Navigieren Sie im Terminal in den Ordner, in dem die .tar-Datei liegt, und führen Sie den folgenden Befehl aus:

```bash
docker load -i drg-language-server-1-3-1.tar
```
Nach erfolgreichem Import kann die .tar-Datei gelöscht werden.

nach dem Import sollte das Image noch neu getagged werden, so dass docker-compose sicher das richtige image findet: 
```bash
docker tag 2647b66ebcd7 gitlab.swissdrg.local:6789/swissdrg/drg-language-server/staging:1.3.1
```

## 3. Lokale Verzeichnisstruktur und Daten
Die Datenbank verknüpft Einträge mit lokalen Dateien, weshalb eine spezifische Ordnerstruktur im Projektverzeichnis benötigt wird.

Erstellen Sie den Pfad storage/egins/.

Erstellen Sie den Pfad storage/workspaces/.

Legen Sie die benötigten EGIN-Dateien (CSV) in den Ordner storage/egins/ ab und die Workspaces (.zip) in storage/workpaces


## 4. Starten der Umgebung
Um die Container zu bauen und zu starten, nutzen Sie diesen Befehl:

```bash
docker-compose up --build
```
Hinweis für den Erststart: Falls das Frontend einen Vite-Fehler erzeugt (da die Library-Ordner initial fehlen), führen Sie einmalig diesen Befehl aus:

```bash
docker-compose run frontend npm install
```
## 5. Datenbank initialisieren
Sobald die Container laufen, muss die Datenbank in einem separaten Terminal-Fenster vorbereitet werden.

Migrationen ausführen:

```bash
docker-compose run --rm backend rails db:migrate
```
Seed ausführen:

```bash
docker-compose run --rm backend rails db:seed
```

Notfall-Reset: Falls die Datenbank zurückgesetzt werden muss, um alte Verknüpfungen zu löschen, nutzen Sie:

```bash
docker-compose run --rm backend rails db:schema:load db:seed
```
## 6. Zugriff und Verifizierung
Nach dem Start sind die Dienste unter folgenden Adressen erreichbar:

Frontend: **http://localhost:3001** (Änderungen werden sofort angezeigt)

Backend: **http://localhost:3000**

DLS Server: **http://localhost:8787**

Test der API
Rufen Sie **http://localhost:3001/api/egins** auf. Sie sollten ein JSON mit den EGIN-Einträgen erhalten.
Zudem können Sie den DLS Server prüfen:

```bash
curl -I http://localhost:8787/batch_evaluate
```
Ein Code 200 bestätigt die korrekte Funktion.

**Das Funktioniert nur wenn die Docker-Container am laufen sind**

## 7. Wichtige Befehle für den Betrieb
Logs einsehen
Um die Backend-Logs (z.B. Caching-Vorgänge des DLS Servers) zu verfolgen:

```bash
docker compose logs -f backend
```
Container beenden:
Beenden Sie die Sitzung mit **STRG+C** oder:

```bash
docker-compose stop
```

Um die Container komplett zu entfernen ("Aufräumen"):
```bash
docker-compose down
```