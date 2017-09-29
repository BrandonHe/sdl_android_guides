# System Capability Manager

The System Capability Manager was built as a universal method where capabilities will be returned for a given key (ie. NAVIGATION, VIDEO_STREAMING). It also alleviates the need to individually cache results that come in the Register App Interface response, or from other areas.

There are multiple capabilites that can be retrieved:

| Supported Capabilities |
| --------- |
| NAVIGATION |
| PHONE_CALL |
| VIDEO_STREAMING |
| REMOTE_CONTROL |
| HMI |
| DISPLAY |
| AUDIO_PASSTHROUGH |
| BUTTON |
| HMI_ZONE |
| PRESET_BANK |
| SOFTBUTTON |
| SPEECH |
| VOICE_RECOGNITION |

## Use

When you receive an `HMIStatus`, you can call the method and pass in the capability that you want.

!!! Note
It is always important to query capabilities. Your app may be used on a variety of head units across different manufacturers and software versions. Never assume that a capability exists.
!!!

For example (obtaining the head unit's `NAVIGATION` capability):

```java
    public void getCapabilities() {

		proxy.getCapability(SystemCapabilityType.NAVIGATION, new OnSystemCapabilityListener(){

			@Override
			public void onCapabilityRetrieved(Object capability){
				NavigationCapability navCapability = (NavigationCapability) capability;
				try {
					Log.i(TAG, "Capability: "+ navCapability.serializeJSON().toString());
				} catch (JSONException e) {
					e.printStackTrace();
				}
			}

			@Override
			public void onError(String info){
				Log.i("Capabilities error", info);
			}
		});
	}
```

The returned capability is easily readable by casting to the capability type you are looking to obtain. From there you can determine whether or not the head unit that the app is connected to can utilize a feature or not. 

#### Lists

There are currently 3 responses that come back as Lists: `AUDIO_PASSTHROUGH`, `BUTTON`, and `SOFTBUTTON`. We've created a method in the `SystemCapabilityManager` to help cast these lists. Below is an example of its usage:


```java
    public void getCapabilities() {

		proxy.getCapability(SystemCapabilityType.BUTTON, new OnSystemCapabilityListener(){

            @Override
            public void onCapabilityRetrieved(Object capability){
                List<ButtonCapabilities> buttonCapabilityList = SystemCapabilityManager.convertToList(capability, ButtonCapabilities.class);

                for (ButtonCapabilities buttonCapability : buttonCapabilityList) {
                    try {
                        Log.i(TAG, "Capabilities: " + buttonCapability.serializeJSON().toString());
                    } catch (JSONException e) {
                        e.printStackTrace();
                    }
                }
            }

			@Override
			public void onError(String info){
				Log.i("Capabilities error", info);
			}
		});
	}
```