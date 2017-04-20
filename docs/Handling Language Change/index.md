#Handling a Language Change 

When a user changes the language on a head unit, the `onProxyClosed()` callback will be called in your app's Sdl Service and your app will disconnect from Core. In order for your app to automatically reconnect to the head unit, there are a few changes to make in the following files: 

* local Sdl Broadcast Receiver
* Android Manifest
* Sdl Service

!!! NOTE
This guide assumes you have set your app up for Bluetooth Multiplexing using the *Upgrading to Multiplexing* guide
!!!

## Sdl Broadcast Receiver
When the Sdl Service's connection to core is closed, we want to tell our local Sdl Broadcast Receiver to restart the Sdl Service. To do this, first add a public action String in your app's local Sdl Broadcast Receiver class that can be broadcasted to:

`public static final String ACTION_LANGUAGE_CHANGED = "ACTION_LANGUAGE_CHANGED";`

Then, override the `onReceive()` method of the local Sdl Broadcast Receiver to call `onSdlEnabled()` when receiving that action:

```
@Override
public void onReceive(Context context, Intent intent) {
	super.onReceive(context, intent); // Required

	if(intent.getAction().equals(ACTION_LANGUAGE_CHANGED)){
		onSdlEnabled(context, intent);
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

## Android Manifest
You need to register your local Sdl Broadcast Receiver for the language change action above. You can do this in your Android Manifest, just add the `ACTION_LANGUAGE_CHANGED` String to the existing intent filter for the Receiver.

```
<receiver android:name="com.company.myapp.LocalSdlReceiver" >
    <intent-filter>
        <action android:name="ACTION_LANGUAGE_CHANGED"/> <!-- New addition -->
        <action android:name="android.intent.action.BOOT_COMPLETED" /> <!-- For multiplexing -->
        <action android:name="android.bluetooth.device.action.ACL_CONNECTED" /> <!-- For multiplexing -->
        <action android:name="android.bluetooth.adapter.action.STATE_CHANGED" /> <!-- For multiplexing -->
        <action android:name="sdl.router.startservice" /> <!-- For multiplexing -->
    </intent-filter>
</receiver>
```

## Sdl Service
Without accounting for a language change, your Sdl Service's `onProxyClosed()` method probably looks similar to this:

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
	if(reason.equals(SdlDisconnectedReason.LANGUAGE_CHANGE)){
		Intent intent = new Intent(SdlReceiver.ACTION_LANGUAGE_CHANGED);
		sendBroadcast(intent);
	}
	stopSelf();
}
```