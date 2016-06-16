# SetDisplayLayout 

Used to set an alternate display layout. If not sent, default screen for given platform will be shown.

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
  			<td>displayLayout</td>
  			<td>string</td>
  			<td>Predefined or dynamically created screen layout.<br> Currently only predefined screen layouts are defined.<br> Predefined layouts include: "ONSCREEN_PRESETS"<br> Custom screen containing app-defined onscreen presets. <br></td>
                  <td>Y</td>
  			<td>maxlength: 500</td>
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