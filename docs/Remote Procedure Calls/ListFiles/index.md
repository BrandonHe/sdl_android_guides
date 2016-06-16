# ListFiles 

Requests the current list of resident filenames for the registered app.

### Request ###

No parameters.

#### Response ###

Returns the current list of resident filenames for the registered app along with the current space available.


### Non-default Result Codes: ###

	- SUCCESS
	- INVALID_DATA
	- OUT_OF_MEMORY
	- TOO_MANY_PENDING_REQUESTS
	- APPLICATION_NOT_REGISTERED
	- GENERIC_ERROR
	- REJECTED