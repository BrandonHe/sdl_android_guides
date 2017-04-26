#Handling a Language Change 

!!! NOTE
This guide applies if your app uses the Multiplex Bluetooth transport
!!!

When a user changes the language on a head unit, the `onProxyClosed()` callback will be called in your app's Sdl Service and your app will disconnect from Core. In order for your app to automatically reconnect to the head unit, there are a few changes to make in the following files: 

* local Sdl Broadcast Receiver
* local Sdl Service

!!! NOTE
This guide assumes you have set your app up for Bluetooth Multiplexing using the *Upgrading to Multiplexing* guide
!!!

## Sdl Broadcast Receiver
When the Sdl Service's connection to core is closed, we want to tell our local Sdl Broadcast Receiver to restart the Sdl Service. To do this, first add a public String in your app's local Sdl Broadcast Receiver class that can be included as an extra in a broadcast intent.

`public static final String RECONNECT_LANG_CHANGE = "RECONNECT_LANG_CHANGE";`

Then, override the `onReceive()` method of the local Sdl Broadcast Receiver to call `onSdlEnabled()` when receiving that action:

```
@Override
public void onReceive(Context context, Intent intent) {
	super.onReceive(context, intent); // Required if overriding this method
	
	if (intent != null) {
		String action = intent.getAction();
		if (action != null){
			if(action.equalsIgnoreCase(TransportConstants.START_ROUTER_SERVICE_ACTION)) {
				if (intent.getBooleanExtra(RECONNECT_LANG_CHANGE, false)) {
					onSdlEnabled(context, intent);
				}
			}
		}
	}
}
```

!!! MUST
Be sure to call `super.onReceive(context, intent);` at the start of the method!
!!!

!!! NOTE
This guide also assumes your local Sdl Broadcast Receiver implements the `onSdlEnabled()` method as follows:
!!!

```
@Override
public void onSdlEnabled(Context context, Intent intent) {
	intent.setClass(context, SdlService.class);
	context.startService(intent);
}
```

## Sdl Service
Without accounting for a language change, your Sdl Service's `onProxyClosed()` method probably looked similar to this:

```
@Override
public void onProxyClosed(String info, Exception e, SdlDisconnectedReason reason) {
	stopSelf();
}
```

We want to tell our local Sdl Broadcast Receiver to restart the service when the reason for closing is a language change. To do so, modify the method as follows: 

```
@Override
public void onProxyClosed(String info, Exception e, SdlDisconnectedReason reason) {
	stopSelf();
	if(reason.equals(SdlDisconnectedReason.LANGUAGE_CHANGE)){
		Intent intent = new Intent(TransportConstants.START_ROUTER_SERVICE_ACTION);
		intent.putExtra(SdlReceiver.RECONNECT_LANG_CHANGE, true);
		sendBroadcast(intent);
	}
}
```