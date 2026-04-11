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