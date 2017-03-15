## Uploading Files and Graphics
Graphics allows for you to better customize what you would like to have your users see, and provide a better User Interface.

When developing an application using SmartDeviceLink, two things must always be remembered when using graphics:

1. You may be connected to a head unit that does not display graphics.
2. You must upload them from your mobile device to Core before using them.

### Detecting if Graphics are Supported
Being able to know if graphics are supported is a very important feature of your application, as this avoids you uploading unneccessary images to the head unit. In order to see if graphics are supported, use the `getDisplayCapabilities()` method of a valid `SdlProxyALM`.

```java
DisplayCapabilities displayCapabilities = proxy.getDisplayCapabilities();
Boolean graphicsSupported = displayCapabilities.getGraphicSupported();
```

### Uploading a File using PutFile
To upload a file to Core, use the `PutFile` RPC. You can use the RPC response callback to subsequently perform certain actions upon a successful or unsuccessful upload. Here's an example that uploads an image to Core and set's the app's HMI icon upon a successful upload.

```java
PutFile putFileRequest = new PutFile();
putFileRequest.setSdlFileName("app_icon.jpeg");
putFileRequest.setFileType(FileType.GRAPHIC_JPEG);
putFileRequest.setPersistentFile(true);
putFileRequest.setFileData(file_data);
putFileRequest.setOnRPCResponseListener(new OnRPCResponseListener() {

    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        setListenerType(UPDATE_LISTENER_TYPE_PUT_FILE); // necessary for PutFile requests

        if(((PutFileResponse) response).getSuccess()){
            try {
                proxy.setappicon("app_icon.jpeg", CorrelationIdGenerator.generateId());
            } catch (SdlException e) {
                e.printStackTrace();
            }
        }else{
            Log.i("SdlService", "Unsuccessful app icon upload.");
        }
    }
});
putFileRequest.setCorrelationID(CorrelationIdGenerator.generateId());
try {
    proxy.sendRPCRequest(putFileRequest);
} catch (SdlException e) {
    e.printStackTrace();
}
```

### File Naming
The file name can only consist of letters (a-Z) and numbers (0-9), otherwise the SDL Core may fail to find the uploaded file (even if it was uploaded successfully).

### File Persistance
PutFile supports uploading persistant images, i.e. images that do not become deleted when your application disconnects. Persistance should be used for images relating to your UI, and not for dynamic aspects, such as Album Artwork.

This relates to the following line in the above example:
```
putFileRequest.setPersistentFile(true);
```

!!! note
Be aware that persistance will not work if space on the head unit is limited.
!!!

### Overwrite Stored Files
If a file being uploaded has the same name as an already uploaded image, the new image will be overwrite the previous image. 

### Check if a File Has Already Been Uploaded
You can use the `ListFiles` RPC to check for the names of uploaded files to avoid this situation.

```java
ListFiles listFiles = new ListFiles();
listFiles.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if(((ListFilesResponse) response).getSuccess()){
            List<String> filenames = ((ListFilesResponse) response).getFilenames();
            if(filenames.contains("app_icon.jpeg")){
                Log.i("SdlService", "App icon is already uploaded.");
            }else{
                Log.i("SdlService", "App icon has not been uploaded.");
            }
        }else{
            Log.i("SdlService", "Failed to request list of uploaded files.");
        }
    }
});
listFiles.setCorrelationID(CorrelationIdGenerator.generateId());
try{
	proxy.sendRPCRequest(listFiles);
} catch (SdlException e) {
	e.printStackTrace();
}
```

### Check the Amount of File Storage
To find the amount of file storage left on the head unit, use the  the `ListFiles` RPC in a similar fashion.

```java
ListFiles listFiles = new ListFiles();
listFiles.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if(((ListFilesResponse) response).getSuccess()){
            Integer spaceAvailable = ((ListFilesResponse) response).getSpaceAvailable();
            Log.i("SdlService", "Space available on Core = " + spaceAvailable);
        }else{
            Log.i("SdlService", "Failed to request list of uploaded files.");
        }
    }
});
listFiles.setCorrelationID(CorrelationIdGenerator.generateId());
try{
	proxy.sendRPCRequest(listFiles);
} catch (SdlException e) {
	e.printStackTrace();
}
```

### Delete Stored Files
Use the `DeleteFile` RPC to delete a file associated with a file name.

```java
DeleteFile deleteFileRequest = new DeleteFile();
deleteFileRequest.setSdlFileName("app_icon.jpeg");
deleteFileRequest.setOnRPCResponseListener(new OnRPCResponseListener() {

    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if(((DeleteFileResponse) response).getSuccess()){
            Log.i("SdlService", "App icon deleted.");
        }else{
            Log.i("SdlService", "Unable to delete app icon.");
        }
    }
});
deleteFileRequest.setCorrelationID(CorrelationIdGenerator.generateId());
try{
	proxy.sendRPCRequest(deleteFileRequest);
} catch (SdlException e) {
	e.printStackTrace();
}
```

## Image Specifics
### Image File Type
Images may be formatted as PNG, JPEG, or BMP. Check the `DisplayCapabilities` object provided by your `SdlProxyALM` to find out what image formats the head unit supports, and specifically the `getImageFields()` method.

### Image Sizes
If an image is uploaded that is larger than the supported size, that image will be scaled down to accomodate.

#### Image Specifications
ImageName 		  	 | Used in RPC				  |	Details 																							  |	Height 		 | Width  | Type
---------------------|----------------------------|-------------------------------------------------------------------------------------------------------|--------------|--------|-------
softButtonImage		 | Show 					  | Will be shown on softbuttons on the base screen														  | 70px         | 70px   | png, jpg, bmp
choiceImage 		 | CreateInteractionChoiceSet | Will be shown in the manual part of an performInteraction either big (ICON_ONLY) or small (LIST_ONLY) | 70px         | 70px   | png, jpg, bmp
choiceSecondaryImage | CreateInteractionChoiceSet | Will be shown on the right side of an entry in (LIST_ONLY) performInteraction						  | 35px 		 | 35px   | png, jpg, bmp
vrHelpItem			 | SetGlobalProperties		  | Will be shown during voice interaction 																  | 35px 		 | 35px   | png, jpg, bmp
menuIcon			 | SetGlobalProperties		  | Will be shown on the “More…” button 																  | 35px 		 | 35px   | png, jpg, bmp
cmdIcon				 | AddCommand				  | Will be shown for commands in the "More…" menu 														  | 35px 		 | 35px   | png, jpg, bmp
appIcon 			 | SetAppIcon				  | Will be shown as Icon in the "Mobile Apps" menu 													  | 70px 		 | 70px   | png, jpg, bmp
graphic 			 | Show 					  | Will be shown on the basescreen as cover art 														  | 185px 		 | 185px  | png, jpg, bmp