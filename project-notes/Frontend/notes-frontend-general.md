# Frontend

## General Tools and Concepts used:

HTML

Javascipt

Typescript

JSX

React

Vite

Node.js

Docker




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
- A running webserver ()




Structure:
>```
>frontend-app
>|-- dockerfile         (specifying the image for the docker container)
>|-- package.json       (specifying the dependencies)
>|-- vite.config.ts     (specifying the configuration for vite)
>|-- index.html         (entry point for the frontend)
>|-- src                (actual source code)
>    |-- main.jsx       (entry point actual code)
>    |-- App.jsx        (main component / root component)
>    |-- components     (components)
>```


Creating new project using vite:
```
npx vite create app-name
```