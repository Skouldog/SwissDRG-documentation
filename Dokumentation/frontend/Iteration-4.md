## Refactoring the SessionData


Since the SessionData handling is no longer optimal or the current Iteration, some small changes are needed to 
make the organisation more efficient.

The structure of the DataType Sessiondata was 
overhauled and is now composed of the following strucutre shown in this diagram:
https://github.com/Skouldog/SwissDRG-documentation/blob/1b902f23a7a58f3a35294c3c6bce74da8363f009/Dokumentation/frontend/Diagramme/SessionData.svg

This new strucutre clearly separates data into 2 distinct categories:

Inputdata: This is all the data needed for the frontend to assemble a backend result request.
ResultData: This is all the data contained in a response coming from the backend.


This allows the two parts to be separated by 2 different contexts.
The table for example then only uses the resultData context.

And the input field only uses the inputdata field.

This then results in the different parts really only refreshing when their respective data has been changed.
