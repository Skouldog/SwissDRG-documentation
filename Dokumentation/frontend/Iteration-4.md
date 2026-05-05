# Iteration 4 - Changelog Frontend

---














&nbsp;

## Refactoring the SessionData and context sharing

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

## feature Admin Page

The admin Page itself is built as a separate react component.
For this the following features need to be implemented:

> manage workspaces
> manage egins
> configure server cache limit
> configure server thread limit




refactor
hooks
adminpanel
