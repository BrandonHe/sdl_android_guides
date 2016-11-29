
# Calling A Phone Number

SDL provides a way to dial a number through its protocol. A connected app can send a single RPC that will start the process of dialing a specific number that was supplied from the app.

!!! NOTE Not all SDL enabled Modules will support this feature. !!!

##DialNumber RPC
The `DialNumber` RPC is the main workhorse around being able to dial a number using SDL. .

```java
DialNumber numberToDial = new DialNumber();

//Set the number that should be dialed
numberToDial.setNumber("867-5309");

numberToDial.setCorrelationID(CorrelationIdGenerator.generateId());

try {
	proxy.sendRPCRequest(numberToDial);
} catch (SdlException e) {
	e.printStackTrace();
}
```
!!! NOTE The phone number set will be sanitized before being set in the `DialNumber` object. All characters shall be stripped from string except digits 0-9 and * # , ; + !!!
