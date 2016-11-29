## Alerts

An alert is a pop-up window with some lines of text and optional soft buttons. When an alert is activated, it will abort any SDL operation that is in-progress, except the already-in-progress alert. If an alert is issued while another alert is still in progress, the newest alert will simply be ignored.

### Dismissing the Alert
The alert will persist on the screen until the timeout has elapsed, or the user dismisses the alert by selecting a button. There is no way to dismiss the alert programmatically other than to set the timeout length.

### Alert UI
Depending the platform, an alert can have up to three lines of text, a progress indicator (e.g. a spinning wheel or hourglass), and up to four soft buttons.
##### SDLAlert without soft buttons
![SDLAlert without soft buttons](assets/SDLAlertWithNoSoftButtons.png "SDLAlert without soft buttons")
##### SDLAlert with soft buttons
![SDLAlert with soft buttons](assets/SDLAlertWithSoftButtons.png "SDLAlert with soft buttons")

### Alert TTS
The alert can also be formatted to speak a prompt when the alert appears on the screen. Do this by setting the `ttsChunks` parameter. To play the alert tone before the text-to-speech is spoken, set `playTone` to `true`.

### Example
```java
//TODO code example

```
