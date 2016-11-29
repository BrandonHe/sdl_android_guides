
# Setting the Navigation Destination

SDL Modules might include built in navigation that can also be enhanced by SDL connected apps. One of these enhancements is setting a navigation destination. Through a few simple calls it becomes easy to prompt the user to set a destination that came from the connected app.

!!! NOTE Not all SDL enabled Modules will support this feature. !!!

##SendLocation RPC
The `SendLocation` RPC is the main workhorse around setting a destination. Most of the parameters are pretty self explanatory on what they mean.

```java
SendLocation location = new SendLocation();
//Set the lat and long of the location that should be set as the destination
location.setLatitudeDegrees(LAT);
location.setLongitudeDegrees(LONG);
location.setLocationName("New Destination");
location.setCorrelationID(CorrelationIdGenerator.generateId());

try {
	proxy.sendRPCRequest(location);
} catch (SdlException e) {
	e.printStackTrace();
}
```