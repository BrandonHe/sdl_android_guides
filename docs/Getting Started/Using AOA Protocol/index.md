#Using Android Open Accessory Protocol
Incorporating AOA into an SDL-enabled app allows it to communicate to a module over USB. This guide will assume the SDL library is already integrated into the app.

We will make changes to: 

* Android Manifest __(of your app)__
* Accessory Filter __(new)__
* SdlService

##Android Manifest
To use the AOA protocol, you must specify so in your app's Manifest with:

```<uses-feature android:name="android.hardware.usb.accessory"/>```

!!! MUST
This feature will not work without including this line!
!!!

Additionally, the SDL Android library houses a USBAccessoryAttachmentActivity that you need to add between your Manifest's `<application>…</application>` tags:

```
<activity android:name="com.smartdevicelink.transport.USBAccessoryAttachmentActivity"
	android:launchMode="singleTop">
	<intent-filter>
		<action android:name="android.hardware.usb.action.USB_ACCESSORY_ATTACHED" />
	</intent-filter>

	<meta-data
		android:name="android.hardware.usb.action.USB_ACCESSORY_ATTACHED"
		android:resource="@xml/accessory_filter" />
</activity>
```

##Accessory Filter (New)
For security purposes, an accessory filter will limit accessory connections to those included in the filter. Add the directory /res/xml to your project and create a new XML file `accessory_filter` inside of it. Add in the following to `accessory_filter`:

```
<?xml version="1.0" encoding="utf-8"?>
<resource>
    <usb-accessory
        manufacturer="SDL"
        model="Core"
        version="1.0"/>
</resource>
```

##SmartDeviceLink Service
When using AOA, the USB Transport instantiation is dependent on an accessory device actually being connected. The following changes need to be made in the `onStartCommand()` function of your SdlService.

AOA is only supported on devices with API level 12 or higher. You can check for this with:

```
if(android.os.Build.VERSION.SDK_INT > android.os.Build.VERSION_CODES.HONEYCOMB){
	// create USB transport
}else{
   Log.e("SdlService", "Unable to start proxy. Android OS version is too low"); // optional
}
```

!!! NOTE
It's recommended to either check for an API level 12 or higher here or specify an API level 12 or higher in your app's Manifest.
!!!

You also need to check that the intent that started your service has an Extra equal to `UsbManager.EXTRA_ACCESSORY`:

```
if(android.os.Build.VERSION.SDK_INT > android.os.Build.VERSION_CODES.HONEYCOMB){
	if(intent!=null && intent.hasExtra(UsbManager.EXTRA_ACCESSORY)) {
		// create USB transport	
	}
}else{
	Log.e("SdlService", "Unable to start proxy. Android OS version is too low"); // optional
}
```

Finally, we can create a new USBTransport with:

```
transport = new USBTransportConfig(getBaseContext(), (UsbAccessory) intent.getParcelableExtra(UsbManager.EXTRA_ACCESSORY), false, false);
```

And our final result is:

```
public int onStartCommand(Intent intent, int flags, int startId) {
        boolean forceConnect = intent !=null && intent.getBooleanExtra(TransportConstants.FORCE_TRANSPORT_CONNECTED, false);
        if (proxy == null) {
            try {
                BaseTransportConfig transport = null;

                if(android.os.Build.VERSION.SDK_INT > android.os.Build.VERSION_CODES.HONEYCOMB){
                    if(intent!=null && intent.hasExtra(UsbManager.EXTRA_ACCESSORY)) {
                        transport = new USBTransportConfig(getBaseContext(), (UsbAccessory) intent.getParcelableExtra(UsbManager.EXTRA_ACCESSORY), false, false); // create USB transport
                    }
                }else{
                    Log.e("SdlService", "Unable to start proxy. Android OS version is too low"); // optional
                }

                if(transport != null){
                    proxy = new SdlProxyALM(this, APP_NAME, true, APP_ID, transport);
                }

            } catch (SdlException e) {
                //There was an error creating the proxy
                if (proxy == null) {
                    //Stop the SdlService
                    stopSelf();
                }
            }
        }else if(forceConnect){
            proxy.forceOnConnected();
        }

        //use START_STICKY because we want the SDLService to be explicitly started and stopped as needed.
        return START_STICKY;
}
```
