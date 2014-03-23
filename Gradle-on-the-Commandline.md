This article will show you how you can run your application from the command line and package it for distribution for the different platforms!

## Setting up ANDROID_HOME
The ANDROID_HOME environment variable needs to be pointing to a valid android SDK before you do any command line wizardry.

Windows: `set ANDROID_HOME=C:/Path/To/Your/Android/Sdk`

Linux, Mac OS X: `export ANDROID_HOME=/Path/To/Your/Android/Sdk`

## Running the project
Gradle let's you easily run a project from the commandline. Just use the gradlew command specify your target platform and the run command for that platform.

### Running the desktop project
`gradlew desktop:run`

This compiles your core and desktop project, and runs the desktop starter. The working directory is the android project's assets folder!

### Running the Android project
`gradlew android:installDebug android:run`

This task will create a debug APK of your application, install it on the first connected emulator or device and start the main activity. The process is split into two tasks because the Android Gradle plugin lets you create multiple flavors of your app (e.g. debug as above, release, ...). You can find more information on the (Android Gradle Plugin site](http://tools.android.com/tech-docs/new-build-system/user-guide).

### Running the iOS project
`gradlew ios:launchIPhoneSimulator`

`gradlew ios:launchIPadSimulator`

`gradlew ios:launchIOSDevice`

The first two commands will launch your app on an iPhone or iPad simualtor, the last command will launch your ios project on a connected iDevice, provided it is provisioned. Please refer to Apple's docs on how to provision a device. Note that the first time you run your iOS project, the compilation will take a long time. Compilation time will decrease significantly on subsequent runs!

### Running the GWT project
`gradlew gwt:superDev`

This will start your application in [GWT Super Dev Mode](http://www.badlogicgames.com/wordpress/?p=3073), which compiles your Java code to Javascript, and allows you to debug your Java code directly in the browser. If you see the message `Next, visit: http://localhost:9876` in your shell, open the browser and navigate to that address. Drag the "Dev Mode On" bookmarklet do your browser bookmarks bar. Next open http://localhost:8080/gwt. This is your application running in the browser! If you change any of your Java code in the core project, just click the bookmarklet, then click "Compile". The changes will take effect in a few seconds. If you modify your assets, you have to restart the server with the above command.

## Packaging the project
Every platform has a different kind of distribution format. In this section we'll see how we can generate those distributions via Gradle.

## Packaging for the desktop
`gradlew desktop:dist`

This will create a runnable JAR file located in the `desktop/build/libs/` folder. It contains all necessary code as well as all your art assets from the android/assets folder and can be run either by double clicking or on the command line via `java -jar jar-file-name.jar`. Your audience must have a JVM installed for this to work. The JAR will work on Windows, Linux and Mac OS X!

## Packaging for Android
`gradlew android:assembleRelease`

