## Setting up ANDROID_HOME
The ANDROID_HOME environment variable needs to be pointing to a valid android SDK before you do any command line wizardry. To do this on Windows, you can issue this from the command line:

`set ANDROID_HOME=C:/Path/To/Your/Android/Sdk`

On Linux and Mac OS X you can do this from the shell:

`export ANDROID_HOME=/Path/To/Your/Android/Sdk`

Once you have the ANDROID_HOME environment variable pointing at the Android SDK, you can use the following to run gradle tasks from the command line:

On Windows invoke gradle like this:
`gradlew clean`

On Mac OS or Linux invoke gradle like this: 
`./gradlew clean`

Note the leading dot slash on Unix like systems. In both cases, the clean task will remove all build files from all modules in the project, e.g. class files previously generated.

## Running the project
Gradle let's you easily run a project from the commandline. Just use the gradlew command specify your target platform and the run command for that platform.

### Running the desktop project
`./gradlew desktop:run`

This compiles your core and desktop project, and runs the desktop starter. The working directory is the android project's assets folder!

### Running the android project
`./gradlew android:installDebug android:run`

This task will create a debug APK of your application, install it on the first connected emulator or device and start the main activity. The process is split into two tasks because the Android Gradle plugin lets you create multiple flavors of your app (e.g. debug as above, release, ...). You can find more information on the (Android Gradle Plugin site](http://tools.android.com/tech-docs/new-build-system/user-guide).

### Running the gwt project
`./gradlew gwt:superDev`

This will start your application in [GWT Super Dev Mode](http://www.badlogicgames.com/wordpress/?p=3073), which compiles your Java code to Javascript, and allows you to debug your Java code directly in the browser. If you see the message `Next, visit: http://localhost:9876` in your shell, open the browser and navigate to that address. Drag the "Dev Mode On" bookmarklet do your browser bookmarks bar. Next open http://localhost:8080/gwt. This is your application running in the browser! If you change any of your Java code in the core project, just click the bookmarklet, then click "Compile". The changes will take effect in a few seconds. If you modify your assets, you have to restart the server with the above command.

### Running the ios project
`./gradlew ios:launchIPhoneSimulator`

`./gradlew ios:launchIPadSimulator`

`./gradlew ios:launchIOSDevice`

The first two commands will launch your app on an iPhone or iPad simualtor, the last command will launch your ios project on a connected iDevice, provided it is provisioned. Please refer to Apple's docs on how to provision a device. Note that the first time you run your iOS project, the compilation will take a long time. Compilation time will decrease significantly on subsequent runs!

### Packaging from the command line
TBD