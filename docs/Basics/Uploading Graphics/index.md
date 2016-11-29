
# Uploading Graphics
Images are a great way to help create a great user experience. SDL allows a developer to send images, display them, and reuse them again or in another locations after being sent.

The position and size of images on the screen is determined by the currently set template. All images must first be uploaded to the SDL Module using the PutFiles  before being used in a Show RPC to then have the images be displayed. Once the image has been successfully uploaded, the app will be notified by the SDL Module.

!!! NOTE Some head units you may be connected to may not support images at all. Please consult the graphicsSupported property in the display capabilities property of the RegisterAppInterface response. !!!

###Image File Type

Images may be formatted as PNG, JPEG, or BMP. Check the displayCapability properties to find out what image formats the head unit supports.

###Image File Name

The image name is a unique id for that image uploaded.

!!! NOTE The image name can only consist of letters (a-Z) and numbers (0-9), otherwise the SDL Core may fail to find the uploaded image (even if it was uploaded successfully). !!!

##Send the PutFile

First, the image itself must be sent to the connected SDL Module. This is done through a PutFile

```java
PutFile putFile = new PutFile();
putFile.setFileType(FileType.GRAPHIC_PNG);
putFile.setSdlFileName("MainImage1");
putFile.setCorrelationID(CorrelationIdGenerator.generateId());
putFile.setSystemFile(false);

//Next set the bytes to send in the PutFile
putFile.setBulkData(mainImage1Bytes);
//Finally, set the listener to know when this PutFile has been sent
putFile.setOnPutFileUpdateListener(listener);
try {
	proxy.sendRPCRequest(putFile);
} catch (SdlException e) {
	e.printStackTrace();
}
```

##Set a PutFile Listener
The image can only be displayed after it has successfully be sent to the SDL Module through a PutFile RPC. SDL offers a way to listen for an RPC Request's response. So if we return to the previous PutFile example

```java
putFile.setOnPutFileUpdateListener(new OnPutFileUpdateListener(){
	@Override
	public void onResponse(int correlationId, RPCResponse response,long totalSize) {
		if(response!= null && response.getSuccess()){
			Log.d(TAG, "Image has been sent!"
		}			
	}		
});
```

##Create the Show RPC
The listener set in the previous example can be used as a trigger to send a Show RPC that will tell the Module to display the image.

```java
putFile.setOnPutFileUpdateListener(new OnPutFileUpdateListener(){
	@Override
	public void onResponse(int correlationId, RPCResponse response,long totalSize) {
		if(response!= null && response.getSuccess()){
			Log.d(TAG, "Image has been sent!"
			//Create the Image object that refers to the putfile that was just sent
			Image image = new Image();
			image.setImageType(ImageType.DYNAMIC);
			image.setValue("mainimage1");
			//Create a Show RPC and send it off
			Show show = new Show();
			show.setGraphic(image);
			show.setCorrelationID(correlationID);
			try {
				proxy.sendRPCRequest(putFile);
			} catch (SdlException e) {
				e.printStackTrace();
			}
		}			
	}			
});
```

##Getting Data of Android Resource Images
App icons and other images might be hardcoded into the app and getting their raw data will be important. The following method will help get that data:

```java
/**
* Helper method to take resource files and turn them into byte arrays
* @param resource Resource file id.
* @return Resulting byte array.
*/
private byte[] contentsOfResource(int resource) {
	InputStream is = null;
	try {
		is = getResources().openRawResource(resource);
		ByteArrayOutputStream os = new ByteArrayOutputStream(is.available());
		final int bufferSize = 4096;
		final byte[] buffer = new byte[bufferSize];
		int available;
		while ((available = is.read(buffer)) >= 0) {
			os.write(buffer, 0, available);
		}
		return os.toByteArray();
	} catch (IOException e) {
		Log.w(TAG, "Can't read icon file", e);
		return null;
	} finally {
		if (is != null) {
			try {
				is.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
```