# Iteration 4 - Changelog Frontend

---














&nbsp;

## Feature: Refactoring the SessionData and context sharing

---

Since the SessionData handling is no longer optimal or the current Iteration, some small changes are needed to 
make the organisation more efficient.

&nbsp;

### Refactoring SessionData Types

The structure of the DataType SessionData was 
overhauled and is now composed of the following structure shown in this diagram:

[svg diagram](https://github.com/Skouldog/SwissDRG-documentation/blob/1b902f23a7a58f3a35294c3c6bce74da8363f009/Dokumentation/frontend/Diagramme/SessionData.svg)

This new strucutre clearly separates data into 2 distinct categories:

`Inputdata`: This is all the data needed for the frontend to assemble a backend result request.

`ResultData`: This is all the data contained in a response coming from the backend.


&nbsp;

### Refactoring Context handling

Using this new type definition of all the relevant sessiondata, the contexts which are used to handle
the sessiondata can be refactored aswell.

From now on the 2 context have clearly separated responsibilities:

`InputDataContext`: handling all data related to the request itself

`ResultDataContext`: handling all data related to the reponse of a request

If this separation is continued troughout the system, re-renderings can be minimized to a minimum.

For the structure of the Sessiondata, a skeleton pattern is used.
In general, the data represents a tree structure.

In adition each type, has a `createXYZ` function simplifying the initiation.


### Acessing data stored in `SessionData`

-> The simplest way to acces the data is using the hooks made for each level of the data tree.
`_-> see in next chapter_`

The `SessionData` is split into the two parts `InputData`and `ResultData`which are then 
stored and made accessible seperately.

They are stored as a state in the `contentPanel` component, which is the lowest common parent 
component of all components using the `SessionData`.


#### How this can be used in a component:

> Make InputData accessible in a component:
> ```
> const { inputData, setInputData } = useInputData();
> ```
> 
> Make ResultData accessible in a component:
> ```
> const { resultData, setResultData } = useResultData();
> ```

_This calls the provider function which then returns a getter and setter_


### Using hooks to access the data

Since `ìnputData` and `resultData` contain nested objects, handling a deeply nested field can get quite complicated.
(because of the structure of typescript and React, this can result in a bunch of boilerplate code)

To make this easier, dedicated hooks where implemented to access a specific layer somewhere down in the tree structure.
These hooks where also made to be "partial", so you can handle single fields and emrging is done internally.

>
>These hooks all have the same form. Here is an example of the ResultInfo Layer:
>
>`useResultInfo();`
>
>When they are called, they directly return a getter and a setter which can then 
>be used to directly acces the component of this layer.
>
>`const { resultInfo, setResultInfo } = useResultInfo();`
> 
>Setting a specific value using the hook then looks as follows:
> 
>`setResultInfo({ durationMS: 10 })`
>

## Additional hooks

`useBackendRequest, useDelayedBackendRequest`

In addition to all the hooks implemented to access data, the hooks handling 
the backend requests were updated as well.
The original backend request hook was adjusted to use the 
new SessionData structure and modified to access the newest state using refs.

In order to eliminate race conditions when React writes new data in 
the SessionData and the backend request might be triggered immediately, an additional delayed hook was added.
It serves as a kind of scheduler. Upon calling, a backend request 
is scheduled for the next time the InputData changes.

This fixes the issue of React using batched state writes, while the request might already be using the data.
 











&nbsp;

&nbsp;

## Feature: Admin Page

---

### Refactoring the structure to feature second separate page

The admin Page itself is built as a separate react component.
To separate the admin comonent from the already build ui component, the structure was furthere refactored.
Now the structure looks as follows:

>```
> src
> |-- pages
> |   |-- searchUI  (ui as it was before)
> |   |   |-- components    (ui components as it was before)
> |   |   |-- hooks         (hooks from before (were all specific to ui))
> |   |   |-- types         (currrently empty since previous types are currently shared)
> |   |   |-- util          (currrently empty since previous util are currently shared)
> |   |
> |   |-- searchAdmin   (component representing the admin page in the future)
> |       |-- hooks
> |       |-- types
> |       |-- util
> |
> |-- shared        (different hooks, styles, types etc, which are not specific to either ui, or admin page)
> |   |-- hooks 
> |   |-- styles
> |   |-- types
> |   |-- utils
> |
> |-- App.tsx (serves as the launcher showing component based on url)
>
>```

Aditionally shortcuts for paths where added. This allows to use:
`@shared` instead of `src/shared`
`@searchUI` instead of `src/pages/searchUI`
`@searchAdmin` instead of `src/pages/searchAdmin`

These have been used to mostly replace relative paths on imports. 
(some relative paths still exist, but this is generally ok in most cases for directly dependent files)


The routing was set up using the `react-router-dom` dependency. 
The configuration now shows the normal ui just as before. 
However, upon adding "/admin" to the url, the admin page gets loaded.

Small sidenote: the compose.yml file had to be edited because even though the dependency
was loaded by the docker build, it was then overwritten by whatever was in the node-modules folder on
the device the person was running the build command.
This basically broke new dependecies if docker containers were not deleted before rebuild.

With the updated file, docker only uses the src folder from the project files in building. Anything else 
is built by the dockerfile instructions.


### Implementing the admin page

For this the following features need to be implemented:

> manage workspaces
> manage egins
> configure server cache limit
> configure server thread limit








## Feature: implemented input verification in frontend

Two hooks were created: 'useInputVerification' and "useInputVerificationService'.

The verification service is basically a wrapper around the parse request that also checks if an 
Egin and Workspace are actually selected before bothering the backend.
-> _There's a debounced version so the backend doesn't get spammed on every keystroke._

All the 'setUpToDate(false) calls that were spread across the input components got ripped out and replaced 
with one central 'useInputChangeHandler hook.
It listens to filter changes in 'InputPanel' and takes care of marking results as outdated, resetting 
verification, and kicking off the debounced check.

The Search and Download buttons now get disabled when the input isn't verified or data is still loading.