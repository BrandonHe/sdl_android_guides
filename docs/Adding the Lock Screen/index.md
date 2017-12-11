# Adding the Lock Screen

In order for your SDL application to be certified with most OEMs you will be required to implement a lock screen on the mobile device. The lock screen will disable user interactions with the application while they are using the head-unit to control application functionality. The URL for the default lock screen image to display is sent by the head-unit.

!!! NOTE
This guide assumes that you have an SDL Service implemented as defined in the [Getting Started](/guides/android/getting-started/) guide.
!!!


## Lock Screen Activity


First, we need to create a new activity, LockScreenActivity, that will host our lock screen. In this activity, we have a static method to register the activity life cycles:

```java
public static void registerActivityLifecycle(Application application) {
        // register only once
        if (ACTIVITY_LIFECYCLE_REGISTERED == false) {
            ACTIVITY_LIFECYCLE_REGISTERED = true;

            // check if API level is >= 14
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.ICE_CREAM_SANDWICH) {
                // create the callback
                ACTIVITY_LIFECYCLE_CALLBACK = new ActivityLifecycleCallbacks() {
                    @Override
                    public void onActivityResumed(Activity activity) {
                        ACTIVITY_RUNNING = true;
                        // recall this method so the lock screen comes up when necessary
                        updateLockScreenStatus(LOCKSCREEN_STATUS);

                        ImageView lockscreenIV = (ImageView) activity.findViewById(R.id.lockscreen);
                        if(lockscreenIcon != null && lockscreenIV != null) {
                            lockscreenIV.setImageBitmap(lockscreenIcon);
                            lockscreenIcon = null;
                        }
                    }

                    @Override
                    public void onActivityPaused(Activity activity) {
                        ACTIVITY_RUNNING = false;
                    }
				//other callback methods . . .
                    
                };

                APPLICATION = application;

                // do the activity registration
                application.registerActivityLifecycleCallbacks(ACTIVITY_LIFECYCLE_CALLBACK);
            } else {
                // fallback and assume we always have an activity
                ACTIVITY_RUNNING = true;
            }
        }
    }
```


The Application class will register the activity life cycles upon application start:

```java
public class SdlApplication extends Application{

    private static final String TAG = SdlApplication.class.getSimpleName();

    @Override
    public void onCreate() {
        super.onCreate();
        LockScreenActivity.registerActivityLifecycle(this);
    }
}
```


Here is the onCreate method of the LockScreenActivity class:

```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_lock_screen);

        LOCKSCREEN_INSTANCE = this;

        // redo the checkup
        updateLockScreenStatus(LOCKSCREEN_STATUS);
    }
```


And the companion activity_lock_screen.xml file:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:gravity="center">
    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:contentDescription="@string/hello_world"
        android:layout_margin="@dimen/activity_horizontal_margin"
        android:src="@drawable/sample_lock"
        android:id="@+id/lockscreen"/>

    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:textSize="16sp"
        android:layout_margin="@dimen/activity_horizontal_margin"
        android:text="@string/hello_world"/>
</LinearLayout>
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


The LockScreenActivity class is included in the [hello_sdl_android](https://github.com/smartdevicelink/hello_sdl_android) project.

## Updating SDL Service
To fully implement a lock screen, you'll want to add a LockScreenManager class as a member of your Service.

```java
public class SdlService extends Service implements IProxyListenerALM{
	//...
	
	private LockScreenManager lockScreenManager = new LockScreenManager();
	
	//...
```


The LockScreenManager class can download and hold a lock screen Bitmap given a URL and a class that implements its callbacks. You can create a class similar to this:

```java
private class LockScreenDownloadedListener implements LockScreenManager.OnLockScreenIconDownloadedListener{

		@Override
		public void onLockScreenIconDownloaded(Bitmap icon) {
			Log.i(TAG, "Lock screen icon downloaded successfully");
		}

		@Override
		public void onLockScreenIconDownloadError(Exception e) {
			Log.e(TAG, "Couldn't download lock screen icon, resorting to default.");
		}
}
```


You should prepare to accept and download the lock screen image from the URL sent by the head-unit in the `onOnSystemRequest` callback (if no customized lock screen image is provided. this will be the image shown on the lock screen, see Customize Your Lock Screen Image section below): 


```java
public void onOnSystemRequest(OnSystemRequest notification) {
		if(notification.getRequestType().equals(RequestType.LOCK_SCREEN_ICON_URL)){
			if(notification.getUrl() != null && lockScreenManager.getLockScreenIcon() == null){
				lockScreenManager.downloadLockScreenIcon(notification.getUrl(), new LockScreenDownloadedListener());
			}
		}
	}
```


## Customize Your Lock Screen Image:

Besides replacing the `android:src=“drawable/sample_lock` field in `activity_lock_screen.xml` with a local image, you can manually set your local custom image for the lock screen by using the static method updateLockScreenImage of LockScreenActivity:

```java
public static void updateLockScreenImage(Bitmap icon){
        lockscreenIcon = icon;
    }
```


Alternatively, you can download your custom image for the lock screen by using the downloadLockScreenIcon method of LockScreenManager:

```java
public void downloadLockScreenIcon(final String url, final OnLockScreenIconDownloadedListener l){
        new Thread(new Runnable(){
            @Override
            public void run(){
                try{
                    lockScreenIcon = HttpUtils.downloadImage(url);
                    if(l != null){
                        l.onLockScreenIconDownloaded(lockScreenIcon);
                    }
                }catch(IOException e){
                    if(l != null){
                        l.onLockScreenIconDownloadError(e);
                    }
                }
            }
        }).start();
    }
```


Now when the HMI fully displays the application, the mobile device will display the lock screen with an image of your choice or a default image downloaded using the URL given by the head-unit.