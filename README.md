ARM mbed M2X MQTT Client
========================

The ARM mbed MQTT client library is used to send/receive data to/from [AT&amp;T's M2X service](https://m2x.att.com/) from [mbed LPC1768](http://mbed.org/platforms/mbed-LPC1768/) or [FRDM-K64F](https://developer.mbed.org/platforms/FRDM-K64F/) microcontrollers using [MQTT](http://mqtt.org/) protocol instead of HTTP protocol.

Getting Started
==========================
1. Signup for an [M2X Account](https://m2x.att.com/signup).
2. Obtain your _Master Key_ from the Master Keys tab of your [Account Settings](https://m2x.att.com/account) screen.
2. Create your first [Device](https://m2x.att.com/devices) and copy its _Device ID_.
3. Review the [M2X API Documentation](https://m2x.att.com/developer/documentation/overview).
4. You have 2 choices for hardware: you can obtain an [mbed LPC1768](http://mbed.org/platforms/mbed-LPC1768/) with an [mbed application board](http://mbed.org/cookbook/mbed-application-board), or you can obtain a [FRDM-K64F](http://www.nxp.com/products/software-and-tools/hardware-development-tools/freedom-development-boards/freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) board.

**NOTE**: Though [similiar boards](http://mbed.org/cookbook/Homepage) exist and may also do the job, this doc is specific to using a combination of mbed LPC1768 microcontroller and mbed application board, or a single FRDM-K64F

**ublox** Modem/GPS shield users can find a m2x working demo with freescale boards on the mbed website [here](http://developer.mbed.org/teams/ublox/code/Cellular_m2x-demo-all/).

Please consult the [M2X glossary](https://m2x.att.com/developer/documentation/glossary) if you have questions about any M2X specific terms.

How to run the examples
=======================

To run the examples, please follow the steps below:

1. Launch [mbed online compiler](https://mbed.org/compiler/) in your browser

2. From within the mbed.org compiler IDE, create the example program by:
   2.1) Right click on 'My Programs' and select 'New Program'.  Select 'Import Library' from the drop-down, then 'From URL'.  
         use https://github.com/jflynn129/WNCInterface_M2XMQTTdemo as the URL to import from.
         This will results in one file being imported, main.cpp.

   2.2) Create the WNCInterface.
         Righ click on the WNCInterface_MQTT_hivemq program entry.  Select 'Import Library' from the drop-down, then 'From URL'.  At
         this point, you will be asked for the Source URL, use https://github.com/jflynn129/WNCInterface.git. The dialog will 
         automatically populate as a Library, set the import name and use 'WNCInterface_HTTP' as the Target Path.  Note that
         the Library name will take the name of the github repository including the ".git", remove the ".git" suffix to 
         successfully import.

   2.2.1) Now import the WNC Controller class.  Right click on the WNCInterface label and select 'Import Library' from 
         the drop-down, then 'From URL'.  Use https://developer.mbed.org/users/fkellermavnet/code/WncControllerK64F/ as the 
         and the other fields will be populated correctly.

   2.3)  Create a MINIMAL-JSON library.
         Righ click on the WNCInterface_MQTT_hivemq program entry.  Select 'Import Library' from the drop-down, then 'From URL'.  
         At this point,  use https://developer.mbed.org/users/defmacro/code/minimal-json/. 

   2.3)  Create a MINIMAL-MQTT library.
         Righ click on the WNCInterface_MQTT_hivemq program entry.  Select 'Import Library' from the drop-down, then 'From URL'.  At
         this point,  use https://developer.mbed.org/users/defmacro/code/minimal-mqtt/. 

   2.4) Create the mbed-rtos and mbed libraries.
         Righ click on the WNCInterface_MQTT_hivemq program entry.  Select 'Import Library' from the drop-down, then 'From 
         Import Wizard'.  At this point, enter 'mbed' in the search box and search.  Multiple entries will be displayed.  
         highlight 'mbed' and 'mbed-rtos' and select 'import'.

3. You now have all the components for the example program. At a minimum you will need to modify a few of the key variables
   such as iCLIENTID and TOPIC.  


Variables used in Examples
==========================

In order to run the given examples, different variables need to be configured. We will walk through those variables in this section.

M2X API Key
-----------

Once you [register](https://m2x.att.com/signup) for an AT&amp;T M2X account, an API key is automatically generated for you. This key is called a _Primary Master Key_ and can be found in the _Master Keys_ tab of your [Account Settings](https://m2x.att.com/account). This key cannot be edited nor deleted, but it can be regenerated. It will give you full access to all APIs.

However, you can also create a _Device API Key_ associated with a given Device, you can use the Device API key to access the streams belonging to that Device.

You can customize this variable in the following line in the examples:

```
char m2xKey[] = "<M2X access key>";
```

Device ID
-------

A device is associated with a source of data. It is a set of data streams, such as streams of locations, temperatures, etc. The following line is needed to configure the device used:

```
char deviceId[] = "<device id>";
```

Stream Name
------------

A stream in a device is a set of timed series data of a specific type (i,e. humidity, temperature), you can use the M2XMQTTClient library to send stream values to M2X server, or receive stream values from M2X server. Use the following line to configure the stream if needed:

```
char streamName[] = "<stream name>";
```

Using the M2XMQTTClient library
=========================

In the M2XMQTTClient, the following API functions are provided:

* `updateStreamValue`: Send stream value to M2X server
* `postDeviceUpdates`: Post values from multiple streams to M2X server
* `updateLocation`: Send location value of a device to M2X server

Returned values
---------------

For all those functions, the HTTP status code will be returned if we can fulfill a HTTP request. For example, `200` will be returned upon success, `401` will be returned if we didn't provide a valid M2X API Key.

Otherwise, the following error codes will be used:

```
static const int E_NOCONNECTION = -1;
static const int E_DISCONNECTED = -2;
static const int E_NOTREACHABLE = -3;
static const int E_INVALID = -4;
static const int E_JSON_INVALID = -5;
```

Update stream value
-------------------

The following functions can be used to post one single value to a stream, which belongs to a device:

```
template <class T>
int updateStreamValue(const char* deviceId, const char* streamName, T value);
```

Here we use C++ templates to generate functions for different types of values, feel free to use values of `float`, `int`, `long` or even `const char*` types here.

NOTE: Our example here is configured to use a temperature sensor for generating values, this only applies to LPC1768 board with the application extention board. If you are using FRDM-K64F board, you might need to change this code or attach a separate temperature sensor.

Post device updates
-------------------

M2X also supports posting multiple values to multiple streams in one call, use the following function for this:

```
template <class T>
int postDeviceUpdates(const char* deviceId, int streamNum,
                      const char* names[], const int counts[],
                      const char* ats[], T values[]);
```

Please refer to the comments in the source code on how to use this function, basically, you need to provide the list of streams you want to post to, and values for each stream.

Update Device Location
--------------------------

You can use the following function to update the location for a device:

```
template <class T>
int updateLocation(const char* deviceId, const char* name,
                   T latitude, T longitude, T elevation);
```

Different from stream values, locations are attached to devices rather than streams. We use templates here, since the values may be in different format, for example, you can express latitudes in both `double` and `const char*`.

How to read Serial output
=========================

Though you can use `printf` on a mbed microcontroller, the output is sent to Serial output. To check what your microcontroller outputs to serial ports, follow the instructions at [here](http://mbed.org/handbook/SerialPC).

Examples
========

We provide a series of examples that will help you get an idea of how to use the `M2XMQTTClient` library to perform all kinds of tasks.

Note that the examples contain fictionary variables, and that they need to be configured as per the instructions above before running on your mbed microcontroller. Each of the examples here also needs an Internet connection setup, either via an Ethernet port or a Wifi module.

In the `UpdateStreamValue` example, the temperature sensor on the mbed application board is needed. For other examples, no extra modules are needed besides the network connection.

UpdateStreamValue
-----------------

This example shows how to post temperatures to M2X server. Before running this, you need to have a valid M2X Key, a device ID and a stream name. The mbed microcontroller need to be hooked on the mbed application board in order to use the temperature sensor.

PostDeviceUpdates
-----------------

This example shows how to post multiple values to multiple streams in one API call.

UpdateLocation
--------------

This one sends location data to M2X server. Idealy a GPS device should be used here to read the cordinates, but for simplicity, we just use pre-set values here to show how to use the API.

License
=======

This library is released under the MIT license. See [`M2XMQTTClient/LICENSE`](M2XMQTTClient/LICENSE) for the terms.

