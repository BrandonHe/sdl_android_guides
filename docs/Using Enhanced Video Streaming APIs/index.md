## Using the Enhanced Video Streaming APIs in Android
The `VirtualDisplayEncoder` class provides a straightforward means for Navigation apps to stream video to the head unit. Fundementally, it allows developers to stream an Android layout file and respond to touch events. This guide will detail how to properly use the methods of this class. The `VirtualDisplayEncoder` class should be used in your app's Sdl Service.

!!! Note
This capability can currently only be used by apps that register to the head unit as Navigation apps.
!!!

### Step One: Creating your own SdlPresentation Class
An extended `SdlPresentation` class will allow you to set the content of the video sent to the head unit as well as respond to touch events on the head unit. Create your own class extending extends `VirtualDisplayEncoder.SdlPresentation`, and be sure to set the Content View to your desired layout file. You can also set `OnClickListeners` for layout elements, or perform similar operations you would in an `Activity`. 

An example of such a class is below. It displays two buttons and an image to the head unit. When a button click is registered by the presentation, it changes the image accordingly.

```
public static class MyPresentation extends VirtualDisplayEncoder.SdlPresentation{

    public MyPresentation(Context context, Display display) {
        super(context, display);
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.stream);

        Button black_button = (Button) findViewById(R.id.black_button);
        black_button.setOnClickListener(customOnClickListener);

        Button red_button = (Button) findViewById(R.id.red_button);
        red_button.setOnClickListener(customOnClickListener);
    }

    View.OnClickListener customOnClickListener = new View.OnClickListener() {

        @Override
        public void onClick(View v) {

            int id = v.getId();
            ImageView imageView = (ImageView) mainView.findViewById(R.id.mainImage);

            if(id == R.id.black_button){
                Log.d(TAG, "Black chip.");
                imageView.setImageResource(R.drawable.blackchip);
            }else if(id == R.id.red_button) {
                Log.d(TAG, "Red chip.");
                imageView.setImageResource(R.drawable.redchip);
            }
        }
    };
}
```

!!! NOTE
To actually route the touch events on the TDK to your presentation, you must follow Step Three.
!!!

### Step Two : Initializing the Encoder
This step describes how to actually use the encoder. First, create a new `VirtualDisplayEncoder` object.

`VirtualDisplayEncoder vdEncoder = new VirtualDisplayEncoder();`

This is an empty constructor. To sufficiently intialize the `VirtualDisplayEncoder`, you must call its `init()` method. This requires you to provide: 

* Your app's `Context`
* An H264 `OutputStream` provided by your Sdl Service's valid `SdlProxyALM` object (this can be obtained by calling `proxy.startH264()`
* Your extended `SdlPresentation` class
* The `ScreenParams` of the head unit you're displaying to (can be obtained by calling `proxy.getDisplayCapabilities().getScreenParams()`)

An example is here:

```
OutputStream sdlOutStream = proxy.startH264(false);
if(sdlOutStream != null){
	virtualDisplayEncoder.init(getApplicationContext(), sdlOutStream, MyPresentation.class, proxy.getDisplayCapabilities().getScreenParams());
}
```

Before starting the encoder, you can OPTIONALLY set the following streaming parameters, overriding their default values (all are Integers): 

 * Frame rate (fps) - desired frame rate for the streaming video
 * Bitrate (bits/sec) - desired bit rate for the streaming video
 * IFrame interval (frames) - desired amount of frames between getting an IFrame
 * Refresh rate (ms) - desired refresh rate of the `SdlPresentation` in milliseconds

Use the `VirtualDisplayEncoder.setStreamParams()` method to set these parameters BEFORE starting the encoder.

```
vdEncoder.init(...);
...

vdEncoder.setStreamParams(24, 512000, 5, 100);

...
vdEncoder.start();
```
	

### Step Three : Processing Touch Events From the Head Unit
When your presentation is actively being displayed to the head unit, you can process and respond to the touches on buttons and other display elements. To do so, you must satisfy these two requirements:

1) Your app's APP ID must have the permission to receive OnTouchEvent notifications. You can apply for an APP ID with this permission by contacting an OEM like [Ford](https://developer.ford.com/pages/develop) or by turning off your development head unit's policy settings. 
2) In your Sdl Service's `onOnTouchEvent()` callback, you must forward the notification to your `VirtualDisplayEncoder` object like so:

```
public void onOnTouchEvent(OnTouchEvent notification) {
    virtualDisplayEncoder.handleTouchEvent(notification);
}
```

### Step Four : Starting the Encoder
You should only start your encoder and begin streaming to the head unit when your app's HMI Level is `HMI_FULL`, and it only needs to be started once. A good place to monitor this is in your Sdl Service's `onOnHMIStatus()` callback. Here's an example of how to start the encoder:

```
public void onOnHMIStatus(OnHMIStatus notification) {
        if(notification.getHmiLevel().equals(HMILevel.HMI_FULL)){
        		if (notification.getFirstRun()) {
        			vdEncoder.start();
        		}
        }
}
```

### Step Five : Shutting down the Encoder
When you are done streaming, you can shut down the encoder using 

`vdEncoder.shutDown();`

Your app will no longer be streaming your `SdlPresentation` to the head unit, or processing touches to it.

!!! NOTE
Shutting down the `VirtualDisplayEncoder` will close the H264 `OutputStream` it was previously provided in its `init()` method.
!!!