Before you can import your project into Eclipse, make sure you [[setup your development environment|Setting up your Development Environment (Eclipse, Intellij IDEA)]]!

## Importing to Eclipse
Using the Gradle project with Eclipse can be done in two ways:

* Generate Eclipse projects on the command line and import them to Eclipse
* Install the Eclipse Gradle Integration and load the Gradle projects directly

### Generating and Importing Eclipse projects
Open the command line, navigate to your project's root folder, then execute

    $ ./gradlew eclipse

This will generate .classpath and .project files for every subproject (core, desktop, android, ios, gwt).

Next, open Eclipse, go to File -> Import -> General -> Existing Projects into Workspace. Click on the Browse button and select your project's root directory. You should see a list of projects (android, core, desktop, gwt, ios), all checked. Click the Finish button.

### Importing with Gradle Integration
If you have installed the [Eclipse Gradle Integration plugin](https://github.com/spring-projects/eclipse-integration-gradle/blob/master/README.md) (check the section 'Installing Gradle tooling from update site") you can work without dropping down onto the command line.

Simple go to File -> Import -> Gradle -> Gradle Project, click Browse and navigate to the root folder of your project, then click Build Model. After a while, you'll see a root project and subprojects (android, core, desktop, gwt, ios). Select all the projects and click Finish.

## Running Your Application ##

  * **Desktop**: Right click the desktop project, `Run As -> Java Application`. Select the desktop starter class (e.g. Main.java).
  * **Android**: make sure you have a device connected and that it shows up in DDMS. If you do not know what this means, please refer to the [Android Developer Guide](http://developer.android.com/guide/index.html). Right click your Android project, `Run As -> Android Application`.
  * **iOS RoboVM**: Right click the robovm project, `Run As -> iOS Device App` to run on a connected device, or `Run As -> iOS Simulator App` to run on the iOS simulator. If you run on a device, you need to [provision](https://developer.apple.com/library/ios/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) it to be able to deploy to it!
  * **HTML5**: there are two modes in which you can run your application.
    * development/hosted mode: runs the actual Java code via GWT plugin magic and allows debugging and hot swapping code (kind of).
      * Right click the HTML5 project, `Run As -> Web Application`. 
      * In the "Web Development" view, click the URL. Your standard browser will open up. If you have not installed the GWT plugin yet, you will be prompted to do so. After installation of the plugin, simply refresh the site in the browser.
    * production/web mode: you will need a web server that serves the compiled files (e.g. [Nginx](http://nginx.org/) or a browser that allows to execute JavaScript from local files.
      * Right click the HTML5 project, `Google -> GWT Compile`
      * Once compiled, place the contents of the war/ directory into a directory your web server can serve. Omit the WEB-INF folder.
      * Point your browser at an URL which will display the `.html` file.

## Debugging Your Application ##

  * **Desktop**: Right click the desktop project, `Debug As -> Java Application`. Select the desktop starter class (e.g. Main.java). Set breakpoints where you need them, hot swap code (edit & save while debugging) to quickly test changes without restarting the app.
  * **Android**: assuming you have a device connected, `Debug As -> Android Application`. Hot swapping is not supported, but you can use the DDMS perspective to check your memory usage, profile your application and so on. Refer to the Android Development Guide for more information.
  * **iOS RoboVM**: debugging is currently not supported by RoboVM
  * **HTML5**:
    * development/hosted mode: runs the actual Java code via GWT plugin magic and allows debugging and hot swapping code (kind of).
      * Right click the HTML5 project, `Debug As -> Web Application`. 
      * In the "Web Development" view, click the URL. Your standard browser will open up. 
      * Set breakpoints in your Java code. If you edit & save code, you'll have to refresh your browser for the changes to take effect. This will essentially restart your application. It's faster than recompiling though.
    * production/web mode: 
      * Right click the HTML5 project, `Google -> GWT Compile`
      * Change the flags in "Advanced" to `-strict -draftCompile` and the "Output Style" to "detailed". This will produce very readable Javascript that you can map almost 1:1 to your Java code.
      * Once compiled, place the contents of the war/ directory into a directory your web server can serve. Omit the WEB-INF folder.
      # Point your browser at an URL which will display the `.html` file.
      # Open up Firebug or similar tools, go to the JavaScript and set breakpoints, watch the callstack and so on. To find a Java Class method, simply search for the package/class/method name.
    * super dev mode: through the power of source maps, you can also debug compiled JS as if it where Java, directly in your browser. See this blog post on [libgdx and super dev mode](http://www.badlogicgames.com/wordpress/?p=3073)