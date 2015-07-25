You just [[generated your libgdx project|Project Setup Gradle]], now it's time to start developing its guts in Intellij IDEA or Android Studio (Android Studio is built on Intellij IDEA)! Before you can import your project into Intellij or Android Studio, make sure you [[setup your development environment|Setting up your Development Environment (Eclipse, Intellij IDEA, NetBeans)]]!

## Importing Your Project
Go to `Import Project`, navigate to your project folder and select the `build.gradle` file. Hit `OK`. In the next dialog, leave all settings as they are and hit `OK` again. It will now import your project. This can take a while on the first time, as it downloads the Gradle wrapper and some dependencies.

### Common Problems
If you run into problems due to a missing validation-api:1.0.0.GA artifact, delete your Maven cache at `C:\Users\username\.m2` or `/Users/username/.m2` or `/home/username/.m2`.

`Unsupported major.minor version 51.0` may occur on Mac OS X. If this is your first project using Intellij IDEA, make sure to define your JDK at the global level: see this [help page](https://www.jetbrains.com/idea/help/configuring-global-project-and-module-sdks.html#d2125997e12) to do so, or alternatively from the welcome screen go to `Configure -> Project Defaults -> Project Structure` then add your JDK in `Platform Settings -> SDKs`.  Otherwise, check out [this article by Serapth](http://www.gamefromscratch.com/post/2014/04/03/Troubleshooting-IntelliJLibGDXRoboVMGradle-issues-on-Mac-OS.aspx) detailing other possible causes and fixes for this issue (just be warned that it is not recommended to edit Intellij's Info.plist, as the app is digitally signed).

If you run into "Error:org.gradle.tooling.GradleConnectionException: Could not execute build using Gradle installation" check project structure ( Ctrl + Alt + Shift + S ) and add your Java JDK to the Project SDK.

## Running Your Project

  * **Desktop**: `Run -> Edit Configurations...`, click the plus (+) button and select `Application`. Set the `Name` to `Desktop`. Set the field `Use classpath of module` to `desktop`, then click on the button of the `Main class` field and select the `DesktopLauncher` class. Set the `Working directory` to your `android/assets/` (or `your_project_path/core/assets/`) folder! Click `Apply` and then `OK`. You have now created a run configuration for your desktop project. You can now select the configuration and run it.
  * **Android**: A configuration for the Android project should be automatically created on project import. As such, you only have to select the configuration and run it!
  * **BlackBerry 10**: You need to have the BlackBerry Android plugin installed (https://developer.blackberry.com/android/documentation/bb_android_studio_plugin_tool.html). You also need a BlackBerry ID signing token, which you can get by following [these](https://developer.blackberry.com/android/documentation/bb_android_studio_plugin_tool.html#alk1340635272845) steps. Right click the project and under BlackBerry Tools -> Start ADB Proxy Manager. In there you can click the play button on your device. After it says "Running at port #49570" the device will show up in the ADB devices list and you can run it just like a real Android device.
  * **iOS**: `Run -> Edit Configurations...`, click the plus (+) button and select `Gradle`. Set the `Name` to `iOS`, set the `Gradle Project` to `ios`, set the `Tasks` to `launchIPhoneSimulator` (alternatives are `launchIPadSimulator` and `launchIOSDevice`for [provisioned devices](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)). Click `Apply` and then `OK`. You have now created a run configuration for your iOS project. You can now select the configuration and run it. The first run will take a bit longer as RoboVM has to compile the entire JDK for iOS. Subsequent runs will compile considerably faster!

  * **HTML**: `View -> Tool Window -> Terminal`, in the terminal, make sure you are in the root folder of your project. Then execute `gradlew.bat html:superDev` (Windows) or `./gradlew html:superDev` (Linux, Mac OS X). This will take a while, as your Java code is compiled to Javascript. Once you see the message `The code server is ready`, fire up your browser and go to  [http://localhost:8080/html](http://localhost:8080/html). This is your app running in the browser! When you change any of your Java code or assets, just click the `SuperDev refresh` button while you are on the site and the server will recompile your code and reload the page! To kill the process, simply press `CTRL + C` in the terminal window.

  **IMPORTANT** The port `9876` is used when doing normal GWT development. Therefore, trying to access the game in any other way than using the port `8080` will not launch your project.

Once this [bug in the Gradle tooling API](http://issues.gradle.org/browse/GRADLE-1539) is fixed, we can simplify running the HTML5 by using the Gradle integration. At the moment, the Gradle process will run forever even if canceled.
## Debugging Your Project
Follow the steps for running the project, but instead of launching via the run (Play) button, launch your configuration via the debug (bug) button. Note that RoboVM currently does not support debugging. Debugging of the html build can be done in the browser as follows:

Run the superDev Gradle task as before. Go to [http://localhost:8080/html](http://localhost:8080/html), click on the `SuperDev Refresh` button and hit `Compile`. In Chrome, press `F12` to bring up the developer tools, go to the sources tab and find the Java file you want to debug. Set breakpoints, step and inspect variables using the power of source maps!

[[images/Screen%20Shot%202014-03-23%20at%2019.11.27-BkaIpjttPQ.png]]

## Packaging your Project
It's easiest to package your application from the command line, or using the Gradle task within Intellij IDEA. To see the relevant Gradle tasks, check the [[Gradle command line documentation|Gradle on the Commandline]].