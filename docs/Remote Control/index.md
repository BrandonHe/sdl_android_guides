# Remote Control

Remote Control provides a framework to allow apps to control certain safe modules within a vehicle.

!!! Note
Not all vehicles have this functionality. Even if they support remote control, you will likely need to request permission from the vehicle manufacturer to use it. 
!!!

#### Why is this helpful?

Consider the following scenarios:

- A radio application wants to use the in-vehicle radio tuner. It needs the functionality to select the radio band (AM/FM/XM/HD/DAB), tune the radio frequency or change the radio station, as well as obtain general radio information for decision making.

- A climate control application needs to turn on the AC, control the air circulation mode, change the fan speed and set the desired cabin temperature.

- A user profile application wants to remember users' favorite settings and apply it later automatically when the users get into the same/another vehicle.

Currently, the Remote Control feature supports these modules:

| Supported RC Modules |
| ---------            |
| Radio                |
| Climate              |

The following table lists what control items are in each control module.

| RC Module | Control Item | Value Range |Type | Comments |
| ------------ | ------------ |------------ | ------------ | ------------ |
| **Radio** | Radio Enabled | true,false  | Get/Set/Notification| read only, all other radio control items need radio enabled to work|
|       | Radio Band | AM,FM,XM  | Get/Set/Notification| |
|       | Radio Frequency | | Get/Set/Notification | value range depends on band |
|       | Radio RDS Data | | Get/Notification | read only |
|       | Available HD Channel | 1-3 | Get/Notification | read only |
|       | Current HD Channel | 1-3 | Get/Set/Notification |
|       | Radio Signal Strength |  | Get/Notification | read only |
|       | Signal Change Threshold |  | Get/Notification | read only |
|       | Radio State | Acquiring, acquired, multicast, not_found | Get/Notification | read only |
| **Climate** | Current Cabin Temperature |  | Get/Notification | read only, value range depends on OEM |
|         | Desired Cabin Temperature |  | Get/Set/Notification | value range depends on OEM |
|         | AC Setting | on, off | Get/Set/Notification |  |
|         | AC MAX Setting | on, off  | Get/Set/Notification |  |
|         | Air Recirculation Setting | on, off  | Get/Set/Notification |  |
|         | Auto AC Mode Setting | on, off  | Get/Set/Notification |  |
|         | Defrost Zone Setting | front, rear, all, none  | Get/Set/Notification |  |
|         | Dual Mode Setting | on, off  | Get/Set/Notification |  |
|         | Fan Speed Setting | 0%-100% | Get/Set/Notification |  |
|         | Ventilation Mode Setting | upper, lower, both, none  | Get/Set/Notification |  |

Remote Control can also allow mobile applications to send simulated button press events for the following common buttons in the vehicle.

The system shall list all available buttons for Remote Control in the `RemoteControlCapabilities`. The capability object will have a List of `ButtonCapabilities` that can be obtained using `getButtonCapabilities()`.

| RC Module | Control Button |
| ------------ | ------------ |
| **Climate** | AC |
|         | AC MAX |
|         | RECIRCULATE |
|         | FAN UP |
|         | FAN DOWN |
|         | TEMPERATURE UP |
|         | TEMPERATURE DOWN |
|         | DEFROST |
|         | DEFROST REAR |
|         | DEFROST MAX |
|         | UPPER VENT |
|         | LOWER VENT |
| **Radio**   | VOLUME UP |
|         | VOLUME DOWN |
|         | EJECT |
|         | SOURCE |
|         | SHUFFLE |
|         | REPEAT |

## Integration

!!! NOTE
For Remote Control to work, the head unit must support SDL Core Version 4.4 or newer
!!!

#### System Capability

!!! MUST 
Prior to using using any Remote Control RPCs, you must check that the head unit has the Remote Control capability. As you may encounter head units that do *not* support it, this check is important.
!!!

To check for this capability, use the following call:

```java
    // First you can check to see if the capability is supported on the module
    if (proxy.isCapabilitySupported(SystemCapabilityType.REMOTE_CONTROL){
		// Since the module does support this capability we can query it for more information
		proxy.getCapability(SystemCapabilityType.REMOTE_CONTROL, new OnSystemCapabilityListener(){

			@Override
			public void onCapabilityRetrieved(Object capability){
				RemoteControlCapabilities remoteControlCapabilities = (RemoteControlCapabilities) capability;
				// Now it is possible to get details on how this capability 
				// is supported using the remoteControlCapabilities object
			}

			@Override
			public void onError(String info){
				Log.i(TAG, "Capability could not be retrieved: "+ info);
			}
		});
	}
```
#### Getting Data

It is possible to retrieve current data relating to these Remote Control modules. The data could be used to store the settings prior to setting them, saving user preferences, etc. Following the check on the system's capability to support Remote Control, we can actually retrieve the data. The following is an example of getting data about the `RADIO` module. It also subscribes to updates to radio data, which will be discussed later on in this guide.

```java
   GetInteriorVehicleData interiorVehicleData = new GetInteriorVehicleData();
   interiorVehicleData.setModuleType(ModuleType.RADIO);
   interiorVehicleData.setSubscribe(TRUE);
   interiorVehicleData.setOnRPCResponseListener(new OnRPCResponseListener() {
		@Override
		public void onResponse(int correlationId, RPCResponse response) {
			GetInteriorVehicleData getResponse = (GetInteriorVehicleData) response;
			//This can now be used to retrieve data
		}
	});

   proxy.sendRPCRequest(interiorVehicleData);

```
#### Setting Data

Of course, the ability to set these modules is the point of Remote Control. Setting data is similar to getting it. Below is an example of setting `ClimateControlData`.

```java
    Temperature temp = new Temperature();
    temp.setUnit(TemperatureUnit.FAHRENHEIT);
    temp.setValue((float) 74.1);

    ClimateControlData climateControlData = new ClimateControlData();
    climateControlData.setAcEnable(TRUE);
    climateControlData.setAcMaxEnable(TRUE);
    climateControlData.setAutoModeEnable(FALSE);
    climateControlData.setCirculateAirEnable(TRUE);
    climateControlData.setCurrentTemperature(temp);
    climateControlData.setDefrostZone(DefrostZone.FRONT);
    climateControlData.setDualModeEnable(TRUE);
    climateControlData.setFanSpeed(2);
    climateControlData.setVentilationMode(VentilationMode.BOTH);
    climateControlData.setDesiredTemperature(temp);

    ModuleData moduleData = new ModuleData();
    moduleData.setModuleType(ModuleType.CLIMATE);
    moduleData.setClimateControlData(climateControlData);

    SetInteriorVehicleData setInteriorVehicleData = new SetInteriorVehicleData();
    setInteriorVehicleData.setModuleData(moduleData);
        
    proxy.sendRPCRequest(setInteriorVehicleData);
```
It is likely that you will not need to set all the data as it is in the example, so if there are settings you don't wish to modify, then you don't have to. 

#### Button Presses

Another unique feature of Remote Control is the ability to send simulated button presses to the associated modules, imitating a button press on the hardware itself.

Simply specify the module, the button, and the type of press you would like:

```java
    ButtonPress buttonPress = new ButtonPress();
    buttonPress.setModuleType(ModuleType.RADIO);
    buttonPress.setButtonName(ButtonName.EJECT);
    buttonPress.setButtonPressMode(ButtonPressMode.SHORT);

    proxy.sendRPCRequest(buttonPress);
  
```
#### Subscribing to changes

It is also possible to subscribe to changes in data associated with supported modules.

To do so, during your `GET` request for data, simply add in `setSubscribe(Boolean)`. To unsubscribe, send the request again with the boolean set to `False`. A code sample for setting the subscription is in the `GET` example above.

The response to a subscription will come in a form of a notification. You can receive this notification through the `IProxyListenerALM` that was supplied to the `SdlProxyALM` object; the method `onOnInteriorVehicleData` will be called when the RPC is received.

##### Using `IProxyListenerALM`
```java
    @Override
    public void onOnInteriorVehicleData(OnInteriorVehicleData response) {
        //Perform action based on notification
    }
```

##### Setting notification Listener
```java
    proxy.addOnRPCNotificationListener(FunctionID.ON_INTERIOR_VEHICLE_DATA, new OnRPCNotificationListener() {
	     @Override
	     public void onNotified(RPCNotification notification) {
	         OnInteriorVehicleData onInteriorVehicleData = (OnInteriorVehicleData)notification;
	         //Perform action based on notification
	     }
    });
```