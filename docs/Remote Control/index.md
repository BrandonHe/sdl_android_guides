# Remote Control

Remote control provides a framework which allows applications to change vehicle settings. These are separated into groups, called modules that have getters / setters / notifications for individual items in that group.

#### Why is this helpful?

Consider the following scenarios:

- A radio application wants to use the in-vehicle radio. It needs the RPC function to select the radio band (AM/FM/XM/HD/DAB), tune the radio frequency or change the radio station, as well as obtain general radio information for decision making.

- A climate control application needs to turn on the AC, control the air circulation mode, change the fan speed and set the desired cabin temperature.

- A user profile application wants to remember user's favorite settings and apply it later automatically when the users get into the same/another vehicle.

Currently, the Remote control supports these modules:

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
|         | AC Setting | on,off | Get/Set/Notification |  |
|         | AC MAX Setting | on,off  | Get/Set/Notification |  |
|         | Air Recirculation Setting | on,off  | Get/Set/Notification |  |
|         | Auto AC Mode Setting | on,off  | Get/Set/Notification |  |
|         | Defrost Zone Setting | front,rear,all,none  | Get/Set/Notification |  |
|         | Dual Mode Setting | on,off  | Get/Set/Notification |  |
|         | Fan Speed Setting | 0%-100% | Get/Set/Notification |  |
|         | Ventilation Mode Setting | upper,lower,both,none  | Get/Set/Notification |  |

Remote Control can also allow mobile applications to send button press event or long press event for the following common climate control buttons in the vehicle.
The system shall list all available RC radio buttons and RC climate buttons in the existing ButtonCapabilities list.

| RC Module | Control Button |
| ------------ | ------------ |
| **Climate** | AC Button |
|         | AC MAX Button |
|         | RECIRCULATE Button |
|         | FAN UP Button |
|         | FAN DOWN Button |
|         | TEMPERATURE UP Button |
|         | TEMPERATURE DOWN Button |
|         | DEFROST Button |
|         | DEFROST REAR Button |
|         | DEFROST MAX Button |
|         | UPPER VENT Button |
|         | LOWER WENT Button |
| **Radio**   | VOLUME UP Button |
|         | VOLUME DOWN Button |
|         | EJECT Button |
|         | SOURCE Button |
|         | SHUFFLE Button |
|         | REPEAT Button |

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
    public void getSystemCapabilities(){
        GetSystemCapability systemCapability = new GetSystemCapability();
        systemCapability.setSystemCapabilityType(SystemCapabilityType.REMOTE_CONTROL);

        try {
            proxy.sendRPCRequest(systemCapability);
        } catch (SdlException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onGetSystemCapabilityResponse(GetSystemCapabilityResponse response) {
        try {
            Log.i(TAG, "GetSystemCapabilityResponse from SDL: " + response.getResultCode().name() +
                    " Info: " + response.getSystemCapability().getSystemCapabilityType() +
                    " OTHER: " + response.getSystemCapability().serializeJSON());
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
```
#### Getting Data

We may want to get data relating to a module in the vehicle. This could be used to store the settings prior to setting them, saving user preferences, etc. Following the check on the system's capability to support Remote Control, we can get the data. The following is an example of getting data about the `RADIO` module. It also subscribes to updates to radio data, which will be discussed later on in this guide.

```java
    public void getInteriorVehicleData() {
        GetInteriorVehicleData interiorVehicleData = new GetInteriorVehicleData();
        interiorVehicleData.setModuleType(ModuleType.RADIO);
        interiorVehicleData.setSubscribe(TRUE);

        try {
            proxy.sendRPCRequest(interiorVehicleData);
        } catch (SdlException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onGetInteriorVehicleDataResponse(GetInteriorVehicleDataResponse response) {
        try {
            Log.i(TAG, "GetInteriorVehicleDataResponse from SDL: " + response.serializeJSON());
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
```
#### Setting Data

Of course, the ability to set these modules is the point of Remote Control. Setting data is similar to getting it. Below is an example of setting `ClimateControlData`.

```java
    public void setInteriorVehicleData() {
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

        try {
            proxy.sendRPCRequest(setInteriorVehicleData);
        } catch (SdlException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onSetInteriorVehicleDataResponse(SetInteriorVehicleDataResponse response) {
        try {
            Log.i(TAG, "SetInteriorVehicleDataResponse from SDL: " + response.serializeJSON());
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
```
Of course, you don't need to set *all* of the data of a module as I did in the example.

#### Button Presses

Another unique feature of Remote Control is the ability to send long and short button presses to the associated modules, imitating a button press on the hardware itself.

Simply specify the module, the button, and the type of press you would like:

```java
    public void sendButtonPress() {
        ButtonPress buttonPress = new ButtonPress();
        buttonPress.setModuleType(ModuleType.RADIO);
        buttonPress.setButtonName(ButtonName.EJECT);
        buttonPress.setButtonPressMode(ButtonPressMode.SHORT);

        try {
            proxy.sendRPCRequest(buttonPress);
        } catch (SdlException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onButtonPressResponse(ButtonPressResponse response) {
        try {
            Log.i(TAG, "ButtonPressResponse from SDL: " + response.serializeJSON());
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
```
#### Subscribing to changes

It is also possible to subscribe to changes in data associated with supported modules.

To do so, during your `GET` request for data, simply add in `setSubscribe(Boolean)`. To unsubscribe, send the request again with the boolean set to `False`. A code sample for setting the subscription is in the `GET` example above.

The response to a subscription will come in a form of a notification. You will need this overridden method to receive the notifications:

```java
    @Override
    public void onOnInteriorVehicleData(OnInteriorVehicleData response) {
        try {
            Log.i(TAG, "OnInteriorVehicleData from SDL: " + response.serializeJSON());
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
```