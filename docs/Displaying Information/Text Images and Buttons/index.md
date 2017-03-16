## Text, Images, and Buttons
All text, images, and buttons on the HMI screen must be sent as part of a `Show` RPC. Subscribe buttons are sent as part of a `SubscribeButton` RPC.

### Text
A maximum of four lines of text can be set in `Show` RPC, however, some templates may only support 1, 2, or 3 lines of text. If all four lines of text are set in the `Show` RPC, but the template only supports three lines of text, then the fourth line will simply be ignored.

```java
Show show = new Show();
show.setMainField1("Hello, this is MainField1.");
show.setMainField2("Hello, this is MainField2.");
show.setMainField3("Hello, this is MainField3.");
show.setMainField4("Hello, this is MainField4.");
show.setCorrelationID(CorrelationIdGenerator.generateId());
show.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if (((ShowResponse) response).getSuccess()) {
            Log.i("SdlService", "Successfully showed.");
        } else {
            Log.i("SdlService", "Show request was rejected.");
        }
    }
});
proxy.sendRPCRequest(show);
```

### Images
The position and size of images on the screen is determined by the currently set template. All images must first be uploaded to the remote system using the `PutFile` RPC before being used in a `Show` RPC. Once the image has been successfully uploaded, the app will be notified in the upload method's completion handler. For information relating to how to upload images, go to the [Uploading Files and Graphics](Uploading Files and Graphics) section.

!!! NOTE
Some head units you may be connected to may not support images at all. Please consult the `getGraphicSupported()` method in the `DisplayCapabilities` property available from the `SdlProxyALM` object.
!!!

#### Show the Image on a Head Unit
Once the image has been uploaded to the head unit, you can show the image on the user interface. To send the image, create an `Image` (com.smartdevicelink.rpc.Image) object, and send it using the `Show` RPC. The `Value` property should be set to the same value used in the `filename` property when it was previously uploaded. Since you have uploaded your own image, the `ImageType` property should be set to `ImageType.DYNAMIC`.

```java
Image image = new Image();
image.setImageType(ImageType.DYNAMIC);
image.setValue("appImage.jpeg"); // a previously uploaded filename using PutFile RPC

Show show = new Show();
show.setGraphic(image);
show.setCorrelationID(CorrelationIdGenerator.generateId());
show.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if (((ShowResponse) response).getSuccess()) {
            Log.i("SdlService", "Successfully showed.");
        } else {
            Log.i("SdlService", "Show request was rejected.");
        }
    }
});
proxy.sendRPCRequest(show);
```

### Soft & Subscribe Buttons
Buttons on the HMI screen are referred to as soft buttons to distinguish them from hard buttons, which are physical buttons on the head unit. Don’t confuse soft buttons with subscribe buttons, which are buttons that can detect user selection on hard buttons (or built-in soft buttons).

#### Soft Buttons
Soft buttons can be created with text, images or both text and images. The location, size, and number of soft buttons visible on the screen depends on the template. If the button has an image, remember to upload the image first to the head unit before setting the image in the `SoftButton` instance.

!!! NOTE
If a button type is set to `SBT_IMAGE` or `SBT_BOTH` but no image is stored on the head unit, the button might not show up on the HMI screen.
!!!

```java
Image cancelImage = new Image();
cancelImage.setImageType(ImageType.DYNAMIC);
cancelImage.setValue("cancel.jpeg");

List<SoftButton> softButtons = new ArrayList<>();

SoftButton yesButton = new SoftButton();
yesButton.setType(SoftButtonType.SBT_TEXT);
yesButton.setText("Yes");

SoftButton cancelButton = new SoftButton();
cancelButton.setType(SoftButtonType.SBT_IMAGE);
cancelButton.setImage(cancelImage);

softButtons.add(yesButton);
softButtons.add(cancelButton);

Show show = new Show();
show.setSoftButtons(softButtons);
show.setCorrelationID(CorrelationIdGenerator.generateId());
show.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if (((ShowResponse) response).getSuccess()) {
            Log.i("SdlService", "Successfully showed.");
        } else {
            Log.i("SdlService", "Show request was rejected.");
        }
    }
});
proxy.sendRPCRequest(show);
```

#### Subscribe Buttons
Subscribe buttons are used to detect changes to hard buttons. You can subscribe to the following hard buttons:

| Button  | Template | Button Type |
| ------------- | ------------- | ------------- |
| Ok (play/pause) | media template only | soft button and hard button |
| Seek left | media template only | soft button and hard button |
| Seek right | media template only | soft button and hard button |
| Tune up | media template only | hard button |
| Tune down | media template only | hard button |
| Preset 0-9 | any template | hard button |
| Search | any template |hard button |
| Custom | any template | hard button |

Audio buttons like the OK (i.e. the `play/pause` button), seek left, seek right, tune up, and tune down buttons can only be used with a media template. The OK, seek left, and seek right buttons will also show up on the screen in a predefined location dictated by the media template on touchscreens. The app will be notified when the user selects the subscribe button on the screen or when the user manipulates the corresponding hard button.

!!! NOTE
You will automatically be assigned the media template if you set your configuration app type as `MEDIA`.
!!!

You can subscribe to buttons using the `SubscribeButton` RPC. 

```java
SubscribeButton subscribeButtonRequest = new SubscribeButton();
subscribeButtonRequest.setButtonName(ButtonName.SEEKRIGHT);
subscribeButtonRequest.setCorrelationID(CorrelationIdGenerator.generateId());
proxy.sendRPCRequest(subscribeButtonRequest);
```