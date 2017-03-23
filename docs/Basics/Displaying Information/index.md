
# Displaying Information


One of the most common tasks a developer will encourter will be displaying information on the module. The RPC related to this task is a Show message. A show encompasses not only text but when to display certain images as well. This guide will simply cover text updates to the module.

##Text Update
A maximum of four lines of text can be set in a Show RPC, however, some templates may only support 1, 2, or 3 lines of text. If all four lines of text are set in the Show RPC, but the template only supports three lines of text, then the fourth line will simply be ignored.

```java
Show show = new Show();
show.setMainField1("This is mainfield 1");
show.setMainField2("This is mainfield 2");
show.setMainField3("This is mainfield 3");
show.setMainField4("This is mainfield 4");
show.setCorrelationID(CorrelationIdGenerator.generateId());

//Send the request through the proxy
proxy.sendRPCRequest(show);
```

