# UnregisterAppInterface
Terminates an application's interface registration. This causes SDL to dispose of all resources associated with the application's interface registration (e.g. Command Menu items, Choice Sets, button subscriptions, etc.).

After the UnregisterAppInterface operation is performed, no other operations can be performed until a new app interface registration is established by calling RegisterAppInterface.

This operation will fail if no interface registration is currently in effect (i.e. no RegisterAppInterface operation was performed prior to this call).

### HMI Status Requirements ###

HMILevel can be FULL, LIMITED, BACKGROUND or NONE.
### Request ###
<table border="1" rules="all">
  		<tr>
  			<th>Name</th>
  			<th>Type</th>
  			<th>Description</th>
                  <th>Reg.</th>
                <th>Notes</th>
  			<th>SmartDeviceLink Version</th>
  		</tr>
  		<tr>
  			<td>gps</td>
  			<td>Boolean</td>
  			<td>GPS data. See GPSData for details</td>
                  <td>N</td>
                 <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
 		<tr>
  			<td>speed</td>
 		<td>Boolean</td>
  			<td>The vehicle speed in kilometers per hour</td>
                  <td>N</td>
                  <td>Subscribable</td>
 			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>rpm</td>
  			<td>Boolean</td>
  			<td>The number of revolutions per minute of the engine</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>fuelLevel</td>
  			<td>Boolean</td>
  			<td>The fuel level in the tank (percentage)</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>fuelLevel_State</td>
  			<td>Boolean</td>
  			<td>The fuel level state</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>instantFuelConsumption</td>
  			<td>Boolean</td>
  			<td>The instantaneous fuel consumption in microlitres</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>externalTemperature</td>
  			<td>Boolean</td>
  			<td>The external temperature in degrees celsius</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>prndl</td>
  			<td>Boolean</td>
  			<td>Currently selected gear.</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>tirePressure</td>
  			<td>Boolean</td>
  			<td>Tire pressure status</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>odometer</td>
  			<td>Boolean</td>
  			<td>Odometer in km</td>
                 <td>N</td>
                  <td>Max Length: 500</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
 			<td>beltStatus</td>
  			<td>Boolean</td>
  			<td>The status of the seat belts</td>
                  <td>N</td>
                 <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>bodyInformation</td>
  			<td>Boolean</td>
  			<td>The body information including ignition status and internal temp</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>deviceStatus</td>
  			<td>Boolean</td>
  			<td>The device status including signal and battery strength</td>
                  <td>N</td>
                  <td>Subscribable</td>
 			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>driverBraking</td>
  			<td>Boolean</td>
  			<td>The status of the brake pedal</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>wiperStatus</td>
  			<td>Boolean</td>
  			<td>The status of the wipers</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>headLampStatus</td>
  			<td>Boolean</td>
  			<td>Status of the head lamps</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>engineTorque</td>
  			<td>Boolean</td>
  			<td>Torque value for engine (in Nm) on non-diesel variants</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>accPedalPosition</td>
  			<td>Boolean</td>
  			<td>Accelerator pedal position (percentage depressed)</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>steeringWheelAngle</td>
  			<td>Boolean</td>
  			<td>Current angle of the steering wheel (in deg)</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>eCallInfo</td>
  			<td>Boolean</td>
  			<td>Emergency Call notification and confirmation data.</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>airbagStatus</td>
  			<td>Boolean</td>
  			<td>The status of the air bags.</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>emergencyEvent</td>
  			<td>Boolean</td>
  			<td>Information related to an emergency event (and if it occurred).</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>clusterModeStatus</td>
  			<td>Boolean</td>
  			<td>The status modes of the instrument panel cluster.</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>myKey</td>
  			<td>Boolean</td>
  			<td>Information related to the MyKey feature.</td>
                  <td>N</td>
                  <td>Subscribable</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  </table>

### Response ###
Once this response is received, no other Operations will be accepted by SDL and no Notifications will be sent by SDL.

#### Non-default Result Codes: ####

    - SUCCESS
	- INVALID_DATA
	- OUT_OF_MEMORY
	- TOO_MANY_PENDING_REQUESTS
	- APPLICATION_NOT_REGISTERED
	- GENERIC_ERROR


### Related Operations ###

RegisterAppInterface

OnAppInterfaceUnregistered

### Example Function Call ###
```java
RPCMessage req;
	req = RPCRequestFactory.buildUnregisterAppInterface(autoIncCorrID++);
	_sdlProxy.sendRPCRequest(req);
```