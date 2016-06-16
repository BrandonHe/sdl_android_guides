# SetAppIcon 

Used to set existing local file on SDL as the app's icon. Not supported on first generation SDL vehicles.

### Request ###

<table border="1" rules="all">
  		<tr>
  			<th>Param Name</th>
  			<th>Type</th>
  			<th>Description</th>
                  <th> Req.</th>
  			<th>Notes</th>
  			<th>Version Available</th>
  		</tr>
  		<tr>
  			<td>SDLFileName</td>
  			<td>String</td>
  			<td>File reference name.</td>
                  <td>Y</td>
  			<td>Maxlength=500</td>
  			<td>SmartDeviceLink 2.0</td>
  		</tr>
   </table>

### Response ###

### Non-default Result Codes: ###

	- SUCCESS
	- INVALID_DATA
	- OUT_OF_MEMORY
	- TOO_MANY_PENDING_REQUESTS
	- APPLICATION_NOT_REGISTERED
	- GENERIC_ERROR
	- REJECTED
