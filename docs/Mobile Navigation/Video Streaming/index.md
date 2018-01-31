## Video Streaming

In order to stream video from an SDL app, we only need to manage a few things. For the most part, the library will handle the majority of logic needed to perform video streaming.

## SDLProxyALM

It is important that we create our `SDLProxyALM` instance with the correct settings in order to stream video. This was already covered in the [Mobile Navigation > Introduction](Mobile Navigation/Introduction).

## SDL Remote Display
The `SdlRemoteDisplay` base class provides the easiest way to start streaming using SDL. The `SdlRemoteDisplay` is extended from Android's `Presentation` class with modifications to work with other aspects of the SDL Android library. 

!!! Note
It is recommended that you extend this as a local class within the service that you have the `SDLProxyALM` instance.
!!!

Extending this class gives developers a familiar, native experience to handling layouts and events on screen.

```java
public static class MyDisplay extends SdlRemoteDisplay{
    public MyDisplay(Context context, Display display) {
        super(context, display);
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.sdl);

        final Button button1 = (Button) findViewById(R.id.button_1);

        button1.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                Log.d(TAG, "Received motion event for button1");
            }
        });
    }
}
```

## Managing the Stream
The only thing left to do to start the stream is combine the `SDLProxyALM` instance and the extension of the `SdlRemoteDisplay`. This should happen when your app receives its first `HMI_FULL` status in the `onOnHMIStatus(OnHMIStatus notification) ` callback. The method that needs to be called is `startRemoteDisplayStream(Context context, final Class<? extends SdlRemoteDisplay> remoteDisplay, final VideoStreamingParameters parameters, final boolean encrypted)` from the `SDLProxyALM`.

```java
@Override
public void onOnHMIStatus(OnHMIStatus notification) {
    if(notification.getHmiLevel().equals(HMILevel.HMI_FULL)){
        if (notification.getFirstRun()) {
            proxy.startRemoteDisplayStream(getApplicationContext(), MyDisplay.class, null, false);
        }
    }
   
}

```

### Ending the Stream
When the `HMIStatus` is back to `HM_NONE` it is time to stop the stream. This is accomplished through a method `stopRemoteDisplayStream()` in the `SDLProxyALM`.
