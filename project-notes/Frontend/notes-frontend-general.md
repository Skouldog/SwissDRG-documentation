# Frontend : Notizen und Erkentnisse

---

&nbsp;

## Generelle Konzepte und Begriffe:

```HTML```: _Layoutsprache_

```Javascipt```: _Programmiersprache_

```Typescript```: _Erwiterung von JavaScript mit Typsicherheit_

```JSX```: _JavaScript Erweiterugn welche HTML und XML als Inline-Syntax erlaubt_

```React```: _JavaScript Bibliothek zum Erstellen von UI_

```vite```: _Build Tool für React (ermöglicht unter anderem die Verwendung von JSX)_

```Node.js```: _JavaScript Runtime Environment (erlaubt das ausführen von JavaScript ausserhalb eines Browsers)_

```Docker```: _Zur ausführung von Anwendungen in einem Container_

&nbsp;

&nbsp;


## Developing the Frontend

---

### Anforderungen:

Das Frontend soll als React Komponent entwickelt werden.
Dadurch soll es möglich sein dieses Komponenten in anderen, bereits bestehenden Anwendungen zu verwenden.

### Was ist React?
> React ist im Grunde nur eine JavaScript Bibliothek, welche dazu gemacht wurde um Benutzeroberflächen zu erstellen
> 
> _React ist a deklarativ, das heisst, der code beschreibt allein wie die UI aufgebaut sein soll.
> React kümmert sich dann automatisch um den Build Prozess und das updaten der UI_
> 
> _neben JavaScript unterstützt React auch TypeScript_

### Was wird benötigt, um eine simple React-App zu erstellen?
- Die React Bibliothek selbst (entweder JavaScript oder TypeScript)
- (Optional) Build Tool wie zb. *Vite* zur Verwendung von JSX anstelle von JS
- Ein lokalen Webserver (Node.js)


## React ohne Build Tool

### Manuelles Erstellen einer Demo React-App

Um eine React App zu erstellen, braucht man das Package-Manager Tool ``npm``.
Dieses Tool ist in Node.js enthalten und muss in den meistne Fällen nicht extra installiert werden.

Mit dem Befehl ```npm -v ```: Überprüfen ob ```npm``` installiert ist


> Manuelles Erstellen eines React Projektordners:
> 
> - Projektordner manuell erstellen: ```mkdir name```
> - In Projektordner navigieren: ```cd name```
> - In diesem Projektordner den npm Package-Manager initialisieren: ```npm init -y```

### Ausführen einer React App ohne Build Tool

### React App ohne Build Tool in einem Docker Container ausführen

### Komponenten erstellen





## React mit ``vite``

### Erstellen einer Demo React-App mit ```vite```

Zwar ist es möglich, ein React Projekt manuell zu erstellen, jedoch 
ist mit ```vite```bereits ein tool enhtalten um dies zu vereinfachen.

```vite``` kann direkt mit ```npm``` aufgerufen werden.

> Erstellen eines React Projekts mit ```vite``` als Build Tool:
> ```
> npm create vite@latest my-app
> ```
>
> Dadurch wird ein neuer Ordner mit den Namen ```my-app``` erstellt.
> Dieser enthält dann direkt die standard Ordnerstruktur eines React Projekts.

### Ausführen einer React App mit ``vite``

Um die React App auszuführen, muss man sich im Root Directory des Projekts befinden.
Dort kann man nun mittels dem Befehl ```npm run dev``` die App in Entwicklungsansicht ausführen.

Dabei sucht dann der Package-Manager ``npm`` nach der Infodatei des Projekts: ``package.json``.
Dort ist neben der Dependency Liste auch vermerkt, welche scripts laufen sollen
falls zb. ``npm run dev`` oder ``npm run build`` aufgerufen wird.

Bei einer App die mit ``vite`` erstellt wurde, sind diese 
Scripts bereits definiert, sodass ``npm run dev`` automatisch folgende Schritte ausführt:

- ``npm`` startet ``vite``
- ``vite`` transkribiert den JSX code in JavaScript.
- ``vite`` startet den lokalen Webserver (node.js)
- ``vite`` wartet auf Änderungen um die App neu zu transkribieren und zu starten.

_``vite`` zeigt dabei den link zur lokalen App direkt in der Knsole an_

Weiter kann die App mit dem Befehl ```npm run build``` kompiliert werden 
und schliesslich mit dem Befehl ```npm run preview``` gestartet werden.


### React App mit ``vite`` in einem Docker Container ausführen








### Komponenten erstellen


