You just [[generated your libgdx project|Project Setup Gradle]], now it's time to start developing its guts in NetBeans! Before you can import your project into NetBeans, make sure you [[setup your development environment|Setting up your Development Environment (Eclipse, Intellij IDEA, NetBeans)]]!

## Importing Your Project
Go to `File -> Open Project...`,å click Browse and navigate to the root folder of your project, then click `Open Project`. NetBeans will now import your project. This can take a while on the first time, as it downloads the Gradle wrapper and some dependencies.

### Common Problems
If you run into problems due to a missing validation-api:1.0.0.GA artifact, delete your Maven cache at `C:\Users\username\.m2` or `/Users/username/.m2` or `/home/username/.m2`.

## Running Your Project

  * **Desktop**: Right click the desktop project, `Run`. You can run it.
  * **Android**: make sure you have a device connected. Right click your Android project, `Tasks -> installDebug`.
  * **iOS RoboVM**: Right click the robovm project, `Tasks -> launchIPhoneSimulator` to run on a connected device(alternatives are `launchIPadSimulator` and `launchIPhoneSimulator `for [provisioned devices](https://developer.apple.com/library/ios/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html))
The first run will take a bit longer as RoboVM has to compile the entire JDK for iOS. Subsequent runs will compile considerably faster!
  * **HTML5**: Right click the gwt project, `Tasks -> superDev`. You can now follow the build process in the console. This will take a while, as your Java code is compiled to Javascript. Once you see the message `The code server is ready`, fire up your browser and go to [http://localhost:9876](http://localhost:9876). Drag the `Dev Mode On`bookmarklet to your browser bar. You only have to do this once! Next, go to [http://localhost:8080/gwt](http://localhost:8080/gwt). This is your app running in the browser! When you change any of your Java code, just click the `Dev Mode On` bookmarklet while you are on the site and the server at port 9876 will recompile your code and reload the page!

## Debugging Your Project ##
Follow the steps for running the project, but instead of launching via `Run`, launch your configuration via `Debug`. Note that RoboVM currently does not support debugging. Debuggin of the GWT build can be done in the browser as follows:

Run the superDev Gradle task as before. Go to [http://localhost:8080/gwt](http://localhost:8080/gwt), click on the `Dev Mode On` boorkmarklet and hit `Compile`. In Chrome, press `F12` to bring up the developer tools, go to the sources tab and find the Java file you want to debug. Set breakpoints, step and inspect variables using the power of source maps!

![super dev mode](http://libgdx.badlogicgames.com/uploads/Screen%20Shot%202014-03-23%20at%2019.11.27-BkaIpjttPQ.png)

## Packaging Your Project
It's easiest to package your application from the command line, or using the Gradle tasks from within NetBeans. To see the relevant Gradle tasks, check the [[Gradle command line documentation|Gradle on the Commandline]].