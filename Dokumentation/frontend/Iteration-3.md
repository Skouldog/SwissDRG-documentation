# Iteration 3 - Changelog Frontend

---










&nbsp;

## Feature: Implementing error message display

---

The Resultdata was refactored to additionally hold a errorMessage. This propperty is optional.
->  Rendering will later try to retrieve it, but if none is available, instead of 
    failing, the frontend should just print a generic errormessage instead
    (should theoretically never occur, but just in case)

In addition to the errorMessage, the previous "isWaiting" variable was refactored into a 
multi-option variable called "state". It can be one of the following:
- "notLoaded"
- "loading" -> corresponds to the previuos implementation of "isWaiting
- "error"
- "loaded"

&nbsp;

The "isUpToDate" propperty is still handled seperatly in a dedicated propperty, since this is independently updated.
The data can still be validly loaded, but fall out of date if input data no longer matches.

This propperty will only be looked at, if dada is actually loaded and has only an effect 
on the table shading and button availability.
->  Since shading and buttons, is only available when data is loaded, the state combinations of "state" and "isUpToDate" 
    where state is not "loaded", are not really handled currently

&nbsp;

The hook which handles backend requests, as well as the hook that handles shortcuts for handling 
resultData states are adjusted, so they set the state accordingly.














&nbsp;

&nbsp;

## Feature: Implementing patient links in the table

---

### Approach:

To make the line elements in the result table clickable, there where 2 main options:

&nbsp;


> #### -> 1) make the entire line element (ResultElement.tsx) clickable using `onClick`
> allows to make the entire row element clickable as one object.

&nbsp;

> #### -> 2) make the results clickabe using `<a>...</a>` and setting the link using `href`
>
> Since the `table`element can not contain `<a>` directly, it is not possible to set the link on a entire row at once.
> As a alternative approach we could make each cell in a table clickabe, and then make the row element (ResultElement.tsx)
> assign the same `href` link to all its cells.
> This way each cell in the same row would link to the same url.

At first option 1 seems very simple. But there is one big disadvantage.

Because our customer is using this patient links to look up data and link to patiants, it might be very usefull to use option 2. 
It allows the renderer of the React app to recognize that this is a link. 
Assuming this React component is rendered in a Browser or similar framework, this enables the following things:
- right click and copy url
- right click and open in new window
- right click and add to favorites

With option 1, this is not directly possible, since the actual linking does not happen in the upfront layer, but is abstracted using hooks for example.

### Implementation:

For now i have implemented the second option.
Every cell has now a link corresponding to the patient.
This link is the same in ever cell of a line.

The base URL is set in the .env file as a environement constant.
It then gets extended with the patient id and set as link.














&nbsp;

&nbsp;

## Feature: Implementing a upgraded page navigation panel

---

In general the new page navigation panel needs to have the previously implemented buttons "previous", "next" as
well as row list of direct buttons for all the pages.

If not all buttons fit, the ones in the middle get replaced wth a "..." button instead.
If clicked, this should open a small dialog to enable direct page access by a entered number.

### Implementation

To simplify the structure, the "prev" and "next" buttons where refactored to be included directly inside of the 
navigationpanel component.

The pagelist component in between those buttons was added. It contains all the code for rendering the direct page buttons.
If a direct page link is clicked, it directly gets loaded. If however, the "..." button gets clicked, a small
dialog popup appears to entera direct number using a number input.

This can be changed if necessary, but currently, this is implemented using the html dialog element as well as some css styling.


### Additional Refactoring

The first additional refactoring was made to unify the button styles.
Now there are 2 button styles in the project: "buttonDark" and "buttonBlue"
These are now in src/styles and can be used everywhere. This enables changes to be made in a central place.
-> The button styles are a template, but properties like "border-radius" can be customized on each usage.
-> for this the classname can be defined as following {classname"buttonDark custombuttonstyle"} : this will apply both styles

Similarly, this was done with styles for Selectors and Inputs as well.

As a bonus, the Dialog style was upgraded with keyframes and a small fadeout handler to allow for smooth transitions.











&nbsp;

&nbsp;

## Feature: Dynamic resizing

---

In order to implement the resizability of the react component, the process turned pout quite simple.
Most of our ui structure was already build with realtive styling in mind. This allowed a simple transition.


The only issue that arose from changing the root component to dynamic size, was that the page navigation panel 
had a small bug with re-rendering. 

With another small fix this was completed with limited code changes. 











&nbsp;

&nbsp;

## Feature: Performance display

---

Just like the resize feature, implementing the time measurement was done with only small changes.
In addition to the hhtp request handling only the string 











&nbsp;

&nbsp;

## Feature: Sorting implementation

---

To tell the backend what how it should sort the data, a single additional parameter is added to the json in the request.
> ... , sorting: { key: ####, direction: #### }, ...

Inside the sorting parameter, there are 2 subParameters: `key` is the name of the column which 
should be used to sort the results, while `direction` can either be "asc" or "desc".

A few examples of how this will look:

> {filter: {workspace_id: 1 ,egin_id: 1, logic_term: "srg in table ( gfa2120no )", per_page: 10, page: 1, sorting: {key: "age", direction: "asc"}}}
>
> {filter: {workspace_id: 1 ,egin_id: 1, logic_term: "srg in table ( gfa2120no )", per_page: 10, page: 1, sorting: {key: "age", direction: "desc"}}}


---

To enable the frontend to send back sorting parameters to the backend, the following steps were taken:

expand input parameters by 2 extra propperties: sorting key and sorting direction.
this was done by creating a single new field called "sorting_config".
This in itself contains the key and the direction.

Adjust backend request hook to include this parameter in the request if it is defined.

To make the headers of the result table control this sorting property, a new headerElement was created.
This is in its core just a Button based on the global button style with some of its obvious button styling removed.
