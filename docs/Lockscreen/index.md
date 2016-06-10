# Lock Screen

In order for your SDL application to be certified with most OEMs you will be required to implement a lock screen on the mobile device. The lock screen will disable user interactions with the application while they are using the head-unit to control application functionality.

!!! NOTE
This guide assumes that you have an SDL Service implemented as defined in the [Getting Started](/guides/android/getting-started/) guide.
!!!

## Lock Screen Activity

First, we need to create a new activity that will host our lock screen. In the activity we add a simple [BroadcastReceiver](https://developer.android.com/reference/android/content/BroadcastReceiver.html) that listens for `CLOSE_LOCK_SCREEN` intents. In the broadcast receiver we will then call [finish()](https://developer.android.com/reference/android/app/Activity.html#finish()) in order to shutdown the activity:

```java
public class LockScreenActivity extends Activity {

    private final BroadcastReceiver closeLockScreenBroadcastReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            finish();
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        registerReceiver(closeLockScreenBroadcastReceiver, new IntentFilter("CLOSE_LOCK_SCREEN"));

        setContentView(R.layout.activity_lock_screen);
    }

    @Override
    protected void onDestroy() {
        unregisterReceiver(closeLockScreenBroadcastReceiver);
        super.onDestroy();
    }
}
```

When the activity is created it will also generate a layout file to use. We updated this layout to display a simple line of text in the middle of the screen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.livio.hellosdl.LockScreenActivity">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:text="@string/lock_screen" />

</RelativeLayout>
```

In our AndroidManifest we need to define the style and launch mode for our lock screen activity. The theme is set to `@android:style/Theme.Black.NoTitleBar.Fullscreen` and the launch mode is set to [singleInstance](https://developer.android.com/guide/topics/manifest/activity-element.html#lmode):

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.livio.hellosdl">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">

        ...

        <activity android:name=".LockScreenActivity"
            android:theme="@android:style/Theme.Black.NoTitleBar.Fullscreen"
            android:launchMode="singleInstance"/>
            
    </application>

</manifest>
```

## Updating SDL Service

Now that our lock screen activity is setup, all we have to do is update our `SdlService` to utilize the LockScreenActivity. First, in the `onOnLockScreenNotification` we need to trigger the lock screen to be displayed with the HMI Level is set to FULL and when the lock screen status is required:

```java
@Override
public void onOnLockScreenNotification(OnLockScreenStatus notification) {
    if(notification.getHMILevel() == HMILevel.HMI_FULL && notification.getShowLockScreen() == LockScreenStatus.REQUIRED) {
        Intent showLockScreenIntent = new Intent(this, LockScreenActivity.class);
        showLockScreenIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(showLockScreenIntent);
    }
}
```

In our `onDestory()` of the `SdlService` we should make sure to clean up the lock screen by broadcasting the `CLOSE_LOCK_SCREEN` intent:

```java
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

    sendBroadcast(new Intent("CLOSE_LOCK_SCREEN"));

    super.onDestroy();
}
```

Now when the HMI displays the application the mobile device will display the lock screen.