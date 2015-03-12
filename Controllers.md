Nate and I have been working on a new extension for gamepads and joysticks. The endresult is gdx-controllers, our first extension to have different backend implementations per platform (other extensions were usually just cross-compiled JNI wrappers, using the same Java code on each platform).

The goal of this extension is to provide the following functionality:
* Enumerate connected controllers
* Support for buttons, axes, sliders, POVs and accelerometers per controller
* Listen for controller events globally or per controller
* Poll controller state

We tried to keep the API as small as possible, with the potential for future additions. No initialization is necessary, the underlying backend will initialize automatically.

##Enumerating Controllers
One or more controllers might be attached to your PC/device. The [Controllers](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-controllers/gdx-controllers/src/com/badlogic/gdx/controllers/Controllers.java) class has methods to query for currently connected devices:

```java
for (Controller controller : Controllers.getControllers()) {
    Gdx.app.log(TAG, controller.getName());
}
```

The order of controllers in the Array returned by Controllers#getControllers() is usually the order in which the controllers were connected. However, you should not rely on this. See the section on (dis-)connecting devices below.

All methods of the Controllers class must be executed on the rendering thread, that is, in any of the methods of ApplicationListener.

## Polling Controller State
Once you have a [Controller](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-controllers/gdx-controllers/src/com/badlogic/gdx/controllers/Controller.java) instance, you can ask it for its current state. A controller can have different components:

* Buttons: this includes your usual ‘X’, ‘Y’ buttons as well as the d-pad on some controllers. A button can be pressed or released.
* Axes: usually provided via analogue sticks. An axes has a value between -1 and 1, with one being the neutral center position.
* POVs (aka [hat switches](http://en.wikipedia.org/wiki/Joystick#Hat_switch)): provide discrete directional state, e.g. north, east, west, south-east and so on.
* Sliders and accelerometers: these are in the API but currently untested.

A classic controller example is the XBox 360 controller:

[[images/300px-Xbox-360-S-Controller.png]]

It has a left and a right analogue stick, each with an x- and a y-axis, triggers on the back, each representing a single axis and various buttons and a d-pad, which might either be reported as buttons or as axes.

Polling for the state of a specific component is pretty simple:

```java
boolean buttonPressed = controller.getButton(buttonCode);
float axisValue = controller.getAxis(axisCode);
...
```

Each component has its own code (analogous to key codes or scan codes for keyboad keys). This code is controller specific, e.g. the X button on an XBox controller likely has a different code than the X button on a PS3 controller. We’ll come back to this issue later.

# Event based Controller Input
When polling a controller, you might lose some input events. For some tasks, event based input is also more suited than polling based input. We hence provide you a way to register listeners to receive controller events, either globally for all controllers, or for a specific controller. The interface you have to implement to listen for controller events is called [ControllerListener](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-controllers/gdx-controllers/src/com/badlogic/gdx/controllers/ControllerListener.java):

```java
public interface ControllerListener {
	public void connected(Controller controller);
	public void disconnected(Controller controller);
	public boolean buttonDown (Controller controller, int buttonCode);
	public boolean buttonUp (Controller controller, int buttonCode);
	public boolean axisMoved (Controller controller, int axisCode, float value);
	public boolean povMoved (Controller controller, int povCode, PovDirection value);
	public boolean xSliderMoved (Controller controller, int sliderCode, boolean value);
	public boolean ySliderMoved (Controller controller, int sliderCode, boolean value);
	public boolean accelerometerMoved (Controller controller, int accelerometerCode, Vector3 value);
}
```

Each method receives the Controller instance for which the event was triggered. The first two methods are called when a controller is connected or disconnected. The next two methods report button press and release events. Both receive a buttonCode identifying the button that was pressed or released. The axisMoved method is called when an axis value changed, and so on. The methods should be pretty self-explanatory.

A ControllerListener can be either added to Controllers, in which case it will listen for events from all controllers, or to a specific Controller, in which case it will only receive events from that controller:

```java
Controllers.addListener(listener); // receives events from all controllers
controller.addListener(listener); // receives events only from this controller
```

If you don’t want to implement all these methods, but only a select few, you can subclass [ControllerAdapter](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-controllers/gdx-controllers/src/com/badlogic/gdx/controllers/ControllerAdapter.java)

```java
controller.addListener(new ControllerAdapter() {
   @Override
   public void connected(Controller controller) {
      ...
   }
 
   public void disconnected(Controller controller) {
      ...
   }
};
```

You might have noticed that some of the listener methods return a boolean. This is used if multiple listeners were registered globally or with a specific controller. If such a method returns false, the event will be handed to the next listener. If the method returns true, the event will not be propagated any further.

All listener methods will be called on the rendering thread, just like in case of a normal InputProcessor.

# Mappings and Codes
As stated above, different components have different codes. These codes are not standardized, so we have two options when dealing with controllers:

* Hardcode the codes for a specific controller, e.g. the Ouya controller
* Let the player configure the controller bindings, e.g. running through each action and asking the player to press the corresponding controller button, axis, POV, etc.

We currently have the [codes for the Ouya controller buttons and axes](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-controllers/gdx-controllers/src/com/badlogic/gdx/controllers/mappings/Ouya.java). To check if a controller is an Ouya controller, you can do this:

```java
if(controller.getName().equals(Ouya.ID)) {
   // we know it's an Ouya controller, so we can use the Ouya codes
   float leftXAxis = controller.getAxis(Ouya.AXIS_LEFT_X);
   boolean oButton = controller.getAxis(Ouya.BUTTON_O);
}
```

You can also check if you are currently running on an Ouya device:

```java
if(Ouya.runningOnOuya) {
   // DO SOMETHING!
}
```

You can pair Ouya controllers with normal Android devices as well, that’s why it might be interesting to know whether you are actually running on an Ouya device.

We’ll add more mappings in the future for popular controllers, e.g. the Xbox 360 and PS3 controllers.

Note that those codes can be different for the same type of controller depending on the operating system. My cheap Logitech controller gets code 0 assigned for its x-axis on Windows, and code 1 on Linux and Mac OS X. For mappings and codes found in the com.badlogic.gdx.controllers.mappings package, like the Ouya class above, we’ll make sure the codes work on all operating systems.

In the end its best to let the user decide how she wants to use her gamepad with your. Writting a simple config dialog that runs through actions and asks the user to press a button is easy to do. You can then save those mappings to a file, in combination with the controller name, and reload and reuse them for the next session of that user.

## Controller (Dis-)connects
Currently controller (dis-)connects are not reported on the desktop. However, they work on Android. If your game supports gamepads, make sure to handle device disconnects and connects! E.g. if a controller disconnects during a game, pause the game and ask the user to reconnect the controller. Note that you will get a brand new Controller instance in that case, so make sure to wire up any listeners correctly. The old controller instance will be reported as disconnected.

## Integration into your project
The core API is found in gdx-controllers.jar, add this to all your Eclipse projects. For the desktop you’ll need to add gdx-controllers-desktop.jar and gdx-controllers-desktop-natives.jar to your desktop project. For Android, you need to add the gdx-controllers-android.jar to your Android project. Make sure you export these jars for your Android project, by going to the properties of the project in Eclipse, Java Build Path -> Order & Export, and make sure all jars are checked. **Note that Android +3.1 is necessary for gamepads to be detected, and Android 4.1 is necessary to get connect/disconnect events.** For older Android versions, nothing will get reported.

## Tests & Demos
I wrote a new little test that lets you display the currently connected controllers and event data, called [ControllersTest](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/extensions/ControllersTest.java). I also augmented the gdx-invaders demo to support Ouya controllers. Note that the integration is a quick and dirty affair which doesn’t handle disconnects or provides any configuration options.