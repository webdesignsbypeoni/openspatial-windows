openspatial-windows
===================

Open Spatial Windows SDK

Open Spatial Service Instructions (v0.0.2)

Welcome to the Open Spatial Windows Service. Here are some basic instructions to get you started.

The package you received will contain 5 items

NodConfig.exe - The service configuration utility.
OpenSpatialDLL.dll - The client DLL
OpenSpatialDLL.lib - The library for the client DLL
OpenSpatialService.exe - The service
OpenSpatialServiceController.h - The header for the DLL
OpenSpatialExampleApplication - a Visual Studio 2013 example project, showcasing how to integrate with the Nod DLL

Note: most of these operations must be done as administrator. If you encounter errors please check you are running everything as an administrator before contacting us.

Part 1: Installing the service

Open an Administrator Command Prompt
Navigate to the folder containing OpenSpatialService.exe

Note: I have seen windows have issues installing services from paths that have a “ “ (space) in the name. if you have issues installing please move
OpenSpatialService.exe into your C:/ root folder.

Type: OpenSpatialService.exe -install
It should print a confirmation message saying the service is installed.

Part 2: Starting the Service and Using the NodConfig application

Connect all your rings to your PC via Bluetooth

Note: If you connect new rings please close and reopen the NodConfig application to
use them.

Open NodConfig.exe (Run as Administrator)
This will start the OpenSpatialService
Click Refresh to list your connected rings
If you select a ring and click settings you can change some settings of the ring

Note: Updating firmware is not supported on windows yet. The UI elements for updating firmware will not do anything

Part 3: Writing your own application with the DLL (using visual studio)
	Note: currently only C++ is supported.

Create a new visual studio project
Copy OpenSpatialDLL.dll, OpenSpatialDLL.lib and OpenSpatialController.h into your source folder
Add OpenSpatialController.h to your solution.

Now you are ready to start using the code. (Please look at the source of the provided visual studio example project if there is any confusion).

There are two C++ classes which are relevant to integrating with Nod.
OpenSpatialServiceController
OpenSpatialDelegate

Step One: Instantiate OpenSpatial Controller

OpenSpatialServiceController is the class which interacts with the OpenSpatialService to control your Nod and get data.

To use this class first create an instance of it. This will automatically setup the connection to the service.

The service works through a subscription / event model. There are 4 types of events
Pointer events - 2D X,Y data
Gesture Events - Swipe Up, Swipe Down etc. These gestures should be explained in your Nod Manual
Button Events - Touches, Tactiles etc, please refer to your Nod Manual for an explanation of the buttons
Pose6D Events - X, Y, Z, Yaw, Pitch, Roll - 3D rotational and translational data. (Currently only rotation euler angles are supported X, Y and Z will always be 0)

To subscribe to these events call the corresponding functions in the OpenSpatialControllerClass:

	void subscribeToPointer(std::string ringName);
	void subscribeToGesture(std::string ringName);
	void subscribeToPose6D(std::string ringName);
	void subscribeToButon(std::string ringName);

These functions take in a ring name string. There is a vector defined as public member of the OpenSpatialController class:

	std::vector<std::string> names;

This vector will contain all the names of your connected rings, so for example to subscribe to Pointer events of the first ring:

	openSpatialController->subscribeToPointer(openSpatialController->names.at(0));

Step Two: Create a Delegate Class

The OpenSpatialDelegate interface defined in OpenSpatialServiceController.h is the class which receives the data from the service.

Create a class which implements this interface. For an example please refer to the ExampleDelegate Class in the example app.

The interface has 4 methods which are called when data is sent from Nod. These functions are in the format:

	virtual void pointerEventFired(PointerEvent event);

Each function will have one event argument which contains the event data. These arguments are structs defined in OpenSpatialServiceController.h

For example the PointerEvent struct contains 3 members. X, the x value of the pointer event. Y, the y value of the pointer event. id, the id of the ring sending the event. This id is an integer starting at 0. It corresponds to the ring at that index in the names vector mentioned earlier.

In these functions implement your handling of the data you need. Notice the example app just prints out the data in a readable format.

Step Three: ASSIGN YOUR DELEGATE!

call the setDelegate function of OpenSpatialServiceController to assign the delegate. Without this you will not receive any data.

e.x. *yourOpenSpatialController* -> setDelegate(*yourOpenSpatialDelegate);

Step Four: Enjoy your data

Data should now be flowing into your application once you subscribe to the appropriate events.

Note: Pointer and Gesture events will only occur in Pointer Mode. Button and Pose6D events will only occur in 3D Mode. Pose6D events will only occur after sliding once on the ring’s slider. Please refer to your Nod Manual to know how to slide.

Thank You for using Nod!

For any questions regarding this windows service or Nod in general please email: support@nod-labs.com
