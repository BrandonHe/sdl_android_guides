# PerformAudioPassThru

This will open an audio pass thru session. By doing so the app can receive audio data through the vehicle’s microphone.

### HMI Status Requirements ###

HMILevel needs to be FULL or LIMITED.

### Request ###

<table border="1" rules="all">
  		<tr>
  			<th>Name</th>
  			<th>Type</th>
  			<th>Description</th>
                  <th>Reg.</th>
                <th>Notes</th>
  			<th> Version</th>
  		</tr>
  		<tr>
  			<td>initialPrompt</td>
  			<td>TTSChunk[]</td>
  			<td>SDL will speak this prompt before opening the audio pass thru session. </td>
                  <td>N</td>
                  <td>This is an array of text chunks of type TTSChunk. The array must have at least one item If omitted, then no initial prompt is spoken: <br> Array Minsize: 1<br> Array Maxsize: 100</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>audioPassThruDisplayText1</td>
  			<td>String</td>
  			<td>First line of text displayed during audio capture.</td>
                  <td>N</td>
                  <td>Maxlength = 500</td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>samplingRate</td>
  			<td>SamplingRate</td>
  			<td>This value shall is allowed to be 8 or 16 or 22 or 44 khz.</td>
                  <td>Y</td>
                  <td></td>
  			<td>SmartDeviceLink 2.0</td>
  		</tr>
  		<tr>
  			<td>maxDuration</td>
  			<td>Integer</td>
  			<td>The maximum duration of audio recording in milliseconds.</td>
                  <td>Y</td>
                  <td>Minvalue: 1<br> Maxvalue: 1000000</td>
  			<td>SmartDeviceLink 2.0</td>
  		</tr>
  		<tr>
  			<td>bitsPerSample</td>
  			<td>BitsPerSample</td>
  			<td>Specifies the quality the audio is recorded - 8 bit or 16 bit.</td>
                  <td>Y</td>
                  <td></td>
  			<td>SmartDeviceLink 2.0 </td>
  		</tr>
  		<tr>
  			<td>audioType</td>
  			<td>AudioType</td>
  			<td>Specifies the type of audio data being requested.</td>
                  <td>Y</td>
                  <td></td>
  			<td>SmartDeviceLink 2.0</td>
  		</tr>
  		<tr>
  			<td>muteAudio</td>
  			<td>Boolean</td>
  			<td>N</td>
                  <td>N</td>
                  <td></td>
  			<td>SmartDeviceLink 2.0</td>
  		</tr>
   </table>

### Response ###

### Non-default Result Codes: ###

	- SUCCESS
	- INVALID_DATA
	- OUT_OF_MEMORY
	- TOO_MANY_PENDING_REQUESTS
	- APPLICATION_NOT_REGISTERED
	- GENERIC_ERROR
	- DISALLOWED
	- REJECTED
	- ABORTED
	- RETRY

### Related Operations ###

* EndAudioPassThru
