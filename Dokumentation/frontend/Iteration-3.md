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




