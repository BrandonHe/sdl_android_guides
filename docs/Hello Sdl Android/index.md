## Hello SDL Android

### Introduction

In this guide we take you through the steps to get our sample project, Hello Sdl Android, running and connected to core. 

First, make sure you download or clone the latest release from [GitHub](https://github.com/smartdevicelink/hello_sdl_android).

Open the project in [Android Studio](https://developer.android.com/studio/index.html). We will exclusively use Android Studio as it is the current supported platform for Android development. 

!!! Note 
The SDL Android Library is packaged in with the Hello SDL Android Project - you will not need to import it
!!!

### Getting Started

If you are not using a Ford TDK for development, we will assume that you have [SDL Core](https://github.com/smartdevicelink/sdl_core) and an [HMI](https://github.com/smartdevicelink/generic_hmi) setup prior to this point. Most people getting started with this tutorial will not have a Ford TDK, sample outputs will be using SDL Core and our Generic HMI.

!!! Note
SDL Core and an HMI are needed to run Hello SDL Android and ensure that it connects
!!!

#### Build Flavors

Hello SDL Android has been built with different **build flavors**, that allow you to test different configurations of the application without modifying much, if any, code. 

To access the Build Variant menu in Android Studio, click on the menu Build -> Select Build Variant. A small window will appear on the bottom left of your IDE window. 

There are many flavors to choose from and for now we will only be concerned with the debug versions. 

Versions Include:

* lbt - Legacy Bluetooth
* mbt - Multiplexing Bluetooth
* tcp - Transmission Control Protocol
* usb - Universal Serial Bus

We will mainly be dealing with mbt (if using a TDK) or tcp (if connecting to SDL Core via a virtual machine or localhost)


 