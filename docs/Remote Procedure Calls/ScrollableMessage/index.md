# ScrollableMessage 

Creates a full screen overlay containing a large block of formatted text that can be scrolled with up to 8 SoftButtons defined.

### HMI Status Requirements ###

HMILevel needs to be FULL.

### Request ###

 <table border="1" rules="all">
 		<tr>
 			<th>Name</th>
 			<th>Type</th>
 			<th>Description</th>
                 <th>Reg.</th>
               <th>Notes</th>
 			<th>Version</th>
 		</tr>
 		<tr>
 			<td>scrollableMessageBody</td>
 			<td>String</td>
 			<td>Body of text that can include newlines and tabs.</td>
                 <td>Y</td>
                 <td></td>
 			<td>SmartDevice Link 1.0 </td>
 		</tr>
 		<tr>
 			<td>timeout</td>
 			<td>Integer</td>
 			<td>App defined timeout.  Indicates how long of a timeout from the last action (i.e. scrolling message resets timeout).</td>
                 <td>N</td>
                 <td>minvalue=1000<br> maxvalue=65535<br> defvalue=30000</td>
 			<td>SmartDevice Link 1.0 </td>
 		</tr>
 		<tr>
 			<td>softButtons</td>
 			<td>SoftButton</td>
 			<td>App defined SoftButtons. If omitted on supported displays, only the system defined "Close" SoftButton will be displayed.</td>
                 <td>N</td>
                 <td>minsize=0<br> maxsize=8</td>
 			<td>SmartDevice Link 1.0 </td>
 		</tr>
  </table>

### Response ###

### Non-default Result Codes: ###

	- SUCCESS
	- INVALID_DATA
	- OUT_OF_MEMORY
	- CHAR_LIMIT_EXCEEDED
	- TOO_MANY_PENDING_REQUESTS
	- APPLICATION_NOT_REGISTERED
	- GENERIC_ERROR
	- DISALLOWED
	- UNSUPPORTED_RESOURCE
	- REJECTED
	- ABORTED