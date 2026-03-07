# Frontend Notes

---

&nbsp;

## General Tools and Concepts used:

```HTML```: _Layout Language_

```Javascipt```: _Programming Language_

```Typescript```: _JavaScript Extension including Type-Safety_

```JSX```: _JavaScript Extension allowing inline HTML and XML_

```React```: _JavaScript Library for building user interfaces_

```vite```: _Build Tool for React (allows using JSX)_

```Node.js```: _JavaScript Runtime Environment (allows running JavaScript outside of a browser)_

```Docker```: _Containerization of Systems to allow for standardizing development environments_

&nbsp;

&nbsp;


## Developing the Frontend

---

### Requirement:

The Frontend should be designed as a React component.
This enables it to be reused and implemented in existing systems.

### What is React?
> React is a JavaScript library for building user interfaces
> 
> _React is a declarative, this means that your code is describing what
the ui should look like. React then takes care of building and
updating the actual UI._


### What is needed to create a simple Demo-Application containing the developed frontend?
- The React library itself (either JavaScript or TypeScript)
- (Optional) build tool like *Vite* allowing to use JSX
- A running webserver (Node.js)


### Demo Frotend erstellen mit React und vite

```npm```: _Node Package Manager welcher in Node.js beinhalten ist_
```npm -v ```: Überprüfen ob ```npm``` installiert ist


> Manuelles Erstellen eines React Projektordners:
> 
> - Projektordner manuell erstellen: ```mkdir name```
> - In Projektordner navigieren: ```cd name```
> - In diesem Projektordner den npm Package-Manager initialisieren: ```npm init -y```


Zwar ist es möglich, ein React Projekt manuell zu erstellen, jedoch 
ist mit ```vite```bereits ein tool enhtalten um dies zu vereinfachen.

```vite``` kann direkt mit ```npm``` aufgerufen werden.

> Erstellen eines React Projekts mit ```vite``` als Build Tool:
> ```
> npm create vite@latest my-app
> ```
>
> This will create a new folder called ```my-app``` containing the 
> basic structure of a React application. 

&nbsp;

> Typische Ordnerstruktur eines Rect Projekts:
> 
> ```
> 
> ```


### Running the application

### Running the application in a docker container

### Building a basic React component


