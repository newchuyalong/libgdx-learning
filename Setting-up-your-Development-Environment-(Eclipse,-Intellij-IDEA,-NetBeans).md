Libgdx projects use [Gradle](http://www.gradle.org/) to manage dependencies, the build process, and IDE integration. This allows you to develop your app with whatever development environment you prefer. Best of all: your team mates can use a different dev environment while working on the same project! Just don't commit your IDE specific files to your source control. The `.gitignore` file contained in libgdx projects will take care of that if you use Git.

### Setup for iOS development
Regardless of your development environment of choice, here's what you'll need for iOS:

  * A Mac with Mac OS X and Xcode 7
  * An [Apple developer account](https://developer.apple.com/membercenter/index.action) if you want to test on device or deploy to the App Store. Please direct all hatred towards Apple.

#### Setting up RoboVM for iOS development
As the official RoboVM has been shut down Microsoft, libGDX has since switched to a fork of RoboVM, maintained by the libGDX core contributors. It's known as [MobiDevelop's RoboVM fork](http://robovm.mobidevelop.com).
jkljkjkjkljlkjklJkdlj;lkjdklasj;lkdajlkj
For Eclipse, check what is the latest release version of the (RoboVM Eclipse plugin](http://robovm.mobidevelop.com/downloads/releases/eclipse/). The update URL should look like this: `http://robovm.mobidevelop.com/downloads/releases/eclipse/2.1.0/site`. Just replace the version string with the latest release' version string.

For IntelliJ IDEA and Android Studio, download the latest [RoboVM IntelliJ IDEA plugin from MobiDevelop's site](http://robovm.mobidevelop.com/downloads/releases/idea/). To install it, run IntelliJ IDEA/Android Studio, hit `CMD+;`, go to `Plugins`, then select `Install from disk...`. Select the `.jar` you downloaded and restart IntelliJ IDEA.

For Gradle, everything is already setup for you. The RoboVM Gradle plugin will be automatically downloaded and used by your Gradle build.

#### Setting up Intel Multi-OS Engine
As an alternative to MobiDevelop's RoboVM fork, libGDX also provides a backend for Intel's Multi-OS Engine. You can download it [here](https://registrationcenter.intel.com/en/forms/?productid=2586). During installation you can install a plugin for IntelliJ IDEA or Android Studio. Intel MOE currently doesn't support Eclipse.

### Setting up Eclipse 
To develop your application via Eclipse, you need to install the following pieces of software.

  * [Java Development Kit 7+ (JDK) (6 will not work!)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
  * [Eclipse](http://www.eclipse.org/downloads/), the "Eclipse IDE for Java Developers" is usually sufficient.
  * [Android SDK](http://developer.android.com/sdk/index.html), you only need the SDK (available at the bottom of the page in the 'command line tools' section), not the whole Android Studio package which is a customized version of Intellij bundled with the Android SDK. Install the latest stable platform via the [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).
  * [Android Development Tools for Eclipse](http://developer.android.com/tools/sdk/eclipse-adt.html), aka ADT Plugin. Use this update site: https://dl-ssl.google.com/android/eclipse/
  * [Eclipse Integration Gradle](https://github.com/spring-projects/eclipse-integration-gradle/), use this update site: http://dist.springsource.com/snapshot/TOOLS/gradle/nightly (for Eclipse 4.4) or http://dist.springsource.com/release/TOOLS/gradle (for Eclipse < 4.4)

To additionally target iOS

  * A Mac, iOS Development does not work on Windows/Linux thanks to Apple.
  * The latest XCode, which you can get from the Mac OS X App Store for free
  * The RoboVM Eclipse plugin as described above.


Once all of these tools are installed, proceed to [[creating your project|Project Setup Gradle]]

### Setting up Intellij IDEA
To develop your application via Intellij IDEA, you need to install the following pieces of software.

  * [Java Development Kit 7+ (JDK) (6 will not work!)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
  * [Intellij IDEA](http://www.jetbrains.com/idea/download/), the Community edition is sufficient.
  * [Android SDK](http://developer.android.com/sdk/index.html), you only need the SDK (available at the bottom of the page in the 'command line tools' section), not the whole Android Studio package which is a customized version of Intellij bundled with the Android SDK. Install the latest stable platform via the [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html). You also have to create an environment variable called ANDROID_HOME, which points at your Android SDK installation directory!

To additionally target iOS

  * A Mac, iOS Development does not work on Windows/Linux thanks to Apple.
  * The latest XCode, which you can get from the Mac OS X App Store for free
  * The RoboVM IntelliJ IDEA plugin or Intel Multi-OS Engine as described above

Once all of these tools are installed, proceed to [[creating your project|Project Setup Gradle]]

### Setting up Android Studio
To develop your application via Android Studio, you need to install the following pieces of software.

  * [Java Development Kit 7+ (JDK) (6 will not work!)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
  * [Android Studio] (https://developer.android.com/sdk/index.html) Android Studio already comes packaged with the Android SDK so contrary to Eclipse or Intellij IDEA you do not need to install this component.

Here is a good [tutorial](http://www.todroid.com/android-gdx-game-creation-part-i-setting-up-up-android-studio-for-creating-games/) on how to install libGDX using a tool provided by Bad Logic.


To additionally target iOS

  * A Mac, iOS Development does not work on Windows/Linux thanks to Apple.
  * The latest XCode, which you can get from the Mac OS X App Store for free
  * The RoboVM IntelliJ IDEA plugin or Intel Multi-OS Engine as described above

Once all of these tools are installed, proceed to [[creating your project|Project Setup Gradle]]


### Setting up NetBeans
To develop your application via NetBeans, you need to install the following pieces of software.

  * [Java Development Kit 7+ (JDK) (6 will not work!)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
  * [NetBeans 7.3+](https://netbeans.org/downloads/), the "Java SE" is sufficient
  * [Android SDK](http://developer.android.com/sdk/index.html), you only need the SDK (available at the bottom of the page in the 'command line tools' section), not the whole Android Studio package which is a customized version of Intellij bundled with the Android SDK. Install the latest stable platform via the [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html). You also have to create an environment variable called ANDROID_HOME, which points at your Android SDK installation directory!
  * [NBAndroid](http://www.nbandroid.org), use this update center: http://nbandroid.org/updates/updates.xml (if you have NetBeans 8.1 or higher, use http://nbandroid.org/release81/updates/updates.xml ).
  * [Gradle Support for NetBeans](https://github.com/kelemen/netbeans-gradle-project), use the NetBeans IDE Update Center.

To additionally target iOS

  * A Mac, iOS Development does not work on Windows/Linux thanks to Apple.
  * The latest XCode, which you can get from the Mac OS X App Store for free

Once all of these tools are installed, proceed to [[creating your project|Project Setup Gradle]]