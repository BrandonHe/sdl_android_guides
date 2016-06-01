
# Installation

#### Gradle

We recommend using [Gradle](http://gradle.org/) for installation. To install the latest version add the following to your `build.gradle`:

```gradle
dependencies {
    compile 'com.smartdevicelink:sdl_android:4.0.1
}
```

TODO Update this when SDL Android is in jcenter

# Getting Started on Android

In this guide, we exclusively use Android Studio. We are going to set-up a bare-bones application so you get started using SDL.

If you just want to jump right into the sample project, the source code can be downloaded [here](TODO)

!!! IMPORTANT
The SDL Mobile library for supports [Android 2.2.x (API Level 8)](https://developer.android.com/about/versions/android-2.2.html) or higher.
!!!
 
## Required System Permissions

In the AndroidManifest for our sample project we need to ensure we have the following system permissions: 

* [Internet](https://developer.android.com/reference/android/Manifest.permission.html#INTERNET) - Used by the mobile library to communicate with a SDL Server
* [Bluetooth](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH) - Primary transport for SDL communication between the device and the vehicle's head-unit
* [Access Network State](https://developer.android.com/reference/android/Manifest.permission.html#ACCESS_NETWORK_STATE) - Required to check if WiFi is enabled on the device

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.company.mySdlApplication">
    
    ...

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.BLUETOOTH"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    ...

</manifest>
```

## SmartDeviceLink Service

A SmartDeviceLink Android Service should be created to manage the lifecycle of an [SDLProxy][SdlProxy]. The SDLService enables auto-start by creating a SDLProxy, which then waits for a connection from SDL. This file also sends and receives messages to and from SDL after connected.

Create a new service and name it appropriately, for this guide we are going to call it `SdlService`. This service must implement the [IProxyListenerALM][IProxyListenerALM] interface:
 
```java
public class SdlService extends Service implements IProxyListenerALM {
    // Inherited methods from IProxyListenerALM
}
```
 The IProxyListenerALM interface has most of the callbacks you will receive during the the SDL proxy's lifecycle. This includes callbacks for RPC responses.
 
If you created the service using the Android Studio template then the service should have been added to your `AndroidManifest.xml` otherwise the service needs to be defined in the manifest:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.company.mySdlApplication">

    <application>
    
    ...

        <service
        android:name=".SdlService"
        android:enabled="true"/>
    
    </application>

    ...

</manifest>
```

### Implementing SDL Proxy Lifecycle

In order to correctly use a proxy developers need to implement methods for the proper creation and disposing of an SDL Proxy in our SDLService.

!!! NOTE
An instance of SdlProxy cannot be reused after it is closed and properly disposed of. Instead, a new instance must be created. Only one instance of SdlProxy should be in use at any given time.
!!!

```java
public class SdlService extends Service implements IProxyListenerALM {

    //The proxy handles communication between the application and SDL
    private SdlProxyALM proxy = null;

    //...

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        if (proxy == null) {
            try {
                //Create a new proxy using Bluetooth transport
                //The listener, app name, 
                //whether or not it is a media app and the applicationId are supplied.
                proxy = new SdlProxyALM(this, "Hello SDL App", true, "8675309");
            } catch (SdlException e) {
                //There was an error creating the proxy
                if (proxy == null) {
                //Stop the SdlService
                    stopSelf();
                }
            }
        }

        mConnectionHandler.postDelayed(mCheckConnectionRunnable, CONNECTION_TIMEOUT);

        //use START_STICKY because we want the SDLService to be explicitly started and stopped as needed.
        return START_STICKY;
    }
    
    @Override
    public void onDestroy() {
        //Dispose of the proxy
        if (proxy != null) {
            try {
                proxy.dispose();
            } catch (SdlException e) {
                e.printStackTrace();
            } finally {
                proxy = null;
            }
        }
        
        super.onDestroy();
    }
    
    @Override
    public void onProxyClosed(String info, Exception e, SdlDisconnectedReason reason) {
        //TODO Explain how best to handle onProxyClosed
        
        //Stop the service
        stopSelf();
    }
    
    //...

}
```

`onProxyClosed()` is called whenever the proxy detects some disconnect in the connection, whether initiated by the app, by SDL, or by the device’s bluetooth connection. As long as the exception does not equal Sdl_PROXY_CYCLED or BLUETOOTH_DISABLED, the proxy would be reset for the exception SDL_PROXY_DISPOSED.

!!! IMPORTANT
We must properly dispose of our proxy in the `onDestory()` method because SDL will issue an error that it lost connection with the app if the connection fails before calling `proxy.displose()`.
!!!

## SmartDeviceLink Broadcast Receiver

The Android implementation of the SdlProxy relies heavily on the OS's bluetooth intents. When the phone is connected to SDL, the app needs to create a SdlProxy, which publishes an SDP record for SDL to connect to. As mentioned previously, a SdlProxy cannot be re-used. When a disconnect between the app and SDL occurs, the current proxy must be disposed of and a new one created.

Create a new BroadcastReceiver and name it appropriately, for this guide we are going to call it `SdlBroadcastReceiver`:

```java
public class SdlBroadcastReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        // ...
    }
}
```

If you created the BroadcastReceiver using the Android Studio template then the service should have been added to your `AndroidManifest.xml` otherwise the receiver needs to be defined in the manifest. Regardless, the manifest needs to be edited so that the `SdlBroadcastReceiver` only needs to respond to the following intents:

* [android.bluetooth.device.action.ACL_CONNECTED](https://developer.android.com/reference/android/bluetooth/BluetoothDevice.html#ACTION_ACL_CONNECTED)
* [android.bluetooth.device.action.ACL_DISCONNECTED](https://developer.android.com/reference/android/bluetooth/BluetoothDevice.html#ACTION_ACL_DISCONNECTED)
* [android.media.AUDIO_BECOMING_NOISY](https://developer.android.com/reference/android/media/AudioManager.html#ACTION_AUDIO_BECOMING_NOISY)

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.company.mySdlApplication">

    <application>

        ...

        <receiver
            android:name=".SdlBroadcastReceiver"
            android:enabled="true">
    
            <intent-filter>
                <action android:name="android.bluetooth.device.action.ACL_CONNECTED" />
                <action android:name="android.bluetooth.device.action.ACL_DISCONNECTED"/>
                <action android:name="android.media.AUDIO_BECOMING_NOISY" />
            </intent-filter>
    
        </receiver>
    
    </application>

...

</manifest>
```

We want to start the SDL Proxy when a phone is connected via bluetooth. We do this by responding to the [android.bluetooth.device.action.ACL_CONNECTED](https://developer.android.com/reference/android/bluetooth/BluetoothDevice.html#ACTION_ACL_CONNECTED) intent:

```java
public class SdlBroadcastReceiver extends BroadcastReceiver {
    public SdlBroadcastReceiver() {
    }

    @Override
    public void onReceive(Context context, Intent intent) {
        final BluetoothDevice bluetoothDevice = (BluetoothDevice) intent.getParcelableExtra(BluetoothDevice.EXTRA_DEVICE);

        // If the device is connected via bluetooth
        if (intent.getAction().compareTo(BluetoothDevice.ACTION_ACL_CONNECTED) == 0) {
            SdlService serviceInstance = SdlService.getInstance();
            if (serviceInstance == null) {
                // Start the SdlService which will start a proxy
                Intent startIntent = new Intent(context, SdlService.class);
                startIntent.putExtras(intent);
                context.startService(startIntent);
            }
        }
        else if (intent.getAction().equals(android.media.AudioManager.ACTION_AUDIO_BECOMING_NOISY)) {
            // signal your service to stop playback
        }
    }
}
```

### Main Activity

Now that the basic connection infrastructure is in place, we should add methods to start the SdlService when our application starts. In `onCreate()` in your main activity, you need to add a method to start the SDL service so that the app is listening for a SDL connection in the case the app is installed while the device is already connected to SDL. (Thus, the app will not receive any Bluetooth events.) In our `MainActivity.java` we need to start the SDLService:

```java
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Start the SDLService
        Intent sdlServiceIntent = new Intent(this, SdlService.class);
        startService(sdlServiceIntent);
    }
}
```

### Implementing IProxyListenerALM Methods

#### onOnHMIStatus()

In our `SdlService`, the `onONHMIStatus()` method is where you should control your application with SDL various [HMI Statuses](todo). When you receive the first [HMI_FULL](todo), you should initialize your app on SDL by subscribing to buttons, registering addcommands, sending an initial show or speak command, etc.

```java
@Override
public void onOnHMIStatus(OnHMIStatus notification) {

    switch(notification.getHmiLevel()) {
        case HMI_FULL:
            //send welcome message, addcommands, subscribe to buttons ect
            break;
        case HMI_LIMITED:
            break;
        case HMI_BACKGROUND:
            break;
        case HMI_NONE:
            break;
        default:
            return;
    }
}
```
