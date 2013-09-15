### To target Windows, Linux, Mac OS X, Android and HTML5 you will need:

  * [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
  * [Eclipse](http://www.eclipse.org/downloads/), the "Eclipse IDE for Java Developers" is usually sufficient.
  * [Android SDK and Eclipse ADT Plugin](http://developer.android.com/sdk/installing.html), simply follow the installation instructions. If you work from source, make sure to install *all* platforms in the Android SDK manager!
  * [Google Web Toolkit](https://developers.google.com/web-toolkit/usingeclipse)

### To additionally target iOS

**RoboVM backend, use this!**
  * A Mac, iOS Development does not work on Windows/Linux thanks to Apple.
  * The latest XCode, which you can get from the Mac OS X App Store for free
  * [RoboVM](http://www.robovm.org/docs.html#eclipse), simply install the Eclipse plugin. Make sure to update the plugin frequently!

Once all of these tools are installed, proceed to [Project Setup](https://github.com/libgdx/libgdx/wiki/Project-setup%2C-running-%26-debugging)

**Xamarin.iOS backend, [deprecated](http://www.badlogicgames.com/wordpress/?p=3156)**
  * ~~A Mac, iOS deployment does not work on Windows/Linux.~~
  * ~~The latest XCode~~
  * ~~[Xamarin.iOS](http://xamarin.com/download), you can use the evaluation license to test on the simulator and device, to deploy to an iOS device or the App Store you will either need a [student license (79$)](http://www.badlogicgames.com/wordpress/?p=2629 ) or a full license (299$)~~
  * ~~[Ant](http://tweedo.com/mirror/apache//ant/binaries/apache-ant-1.8.4-bin.zip). Download and extract the zip to say /Users/you/ant, then create a symlink via `ln -s /Users/you/ant/bin/ant /usr/bin/ant`~~
  * ~~Make sure both `javac` and `ant` can be executed from the command line!~~


### To work directly from the [source repository](https://github.com/libgdx/libgdx)**
  * A Git client to fetch the source from the repository.
  * [Ant](http://ant.apache.org/bindownload.cgi). Download and extract the zip, and make sure you can execute `ant` from the command line by adding its bin/ folder to your PATH environment variable.
  * `javac` needs to be in your PATH
  * `JAVA_HOME` needs to be set to your JDK installation directory.  
  * If you want to also compile the native C/C++ sources, you will need compilers for each platform. See SourceBuilding for more info.