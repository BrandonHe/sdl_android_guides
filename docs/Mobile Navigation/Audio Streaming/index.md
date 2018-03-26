## Audio Streaming

During navigation, it is common for directions, alerts, or warnings to be played to allow the user to make decisions safely, without requiring them to look away from the road. This will cover how navigation apps utilizing SDL can play audio snippets while streaming their app.

!!! Note
For streaming consistent audio, such as music, use a normal A2DP stream and not this method.
!!!

#### Streaming Audio 

To stream audio, we call `proxy.startAudioStream()` which will return an `IAudioStreamListener` object that we will use to send the audio. This should happen when your app is in `HMI_FULL` status in the `onOnHMIStatus(OnHMIStatus notification) ` callback.

For example: 

```java
    private void startAudioStream(){

		final InputStream is = getResources().openRawResource(R.raw.audio_resource);

    	AudioStreamingParams audioParams = new AudioStreamingParams(44100, 1);
		IAudioStreamListener listener = proxy.startAudioStream(false, AudioStreamingCodec.LPCM, audioParams);
		if (listener != null){
			try {
				listener.sendAudio(readToByteBuffer(is), -1);
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}

	private static ByteBuffer readToByteBuffer(InputStream inStream) throws IOException {
		byte[] buffer = new byte[8000];
		ByteArrayOutputStream outStream = new ByteArrayOutputStream(8000);
		int read;
		while (true) {
			read = inStream.read(buffer);
			if (read == -1) {
				break;
			}
			outStream.write(buffer, 0, read);
		}
		return ByteBuffer.wrap(outStream.toByteArray());
	}
```

#### Stopping the Audio Stream

When the stream is complete, or you receive HMI_NONE, you should stop the stream by calling 

```java
	private void stopAudioStream() {
		proxy.endAudioStream();
	}
```
