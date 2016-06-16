# DeleteFile

Used to delete a file resident on the SDL module in the app's local cache.
Not supported on first generation SDL vehicles.

### HMI Status Requirements ###


### Request ###
<table border="1" rules="all">
  		<tr>
  			<th>Name</th>
  			<th>Type</th>
  			<th>Description</th>
                  <th>Reg.</th>
                <th>Notes</th>
  			<th>SmartDeviceLink 2.0</th>
  		</tr>
  		<tr>
  			<td>SDLFileName</td>
  			<td>String</td>
  			<td> File reference name.</td>
                  <td>Y</td>
                  <td>maxlength:500</td>
  			<td>SmartDeviceLink 2.0</td>
  		</tr>
   </table>

### Response ###

### Non-default Result Codes: ###

* SUCCESS
* INVALID_DATA
* OUT_OF_MEMORY
* TOO_MANY_PENDING_REQUESTS
* APPLICATION_NOT_REGISTERED
* GENERIC_ERROR
* REJECTED

### Related Operations ###

* PutFile
* ListFiles