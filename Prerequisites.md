### To target Windows, Linux, Mac OS X, Android and HTML5 you will need:

  * [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
  * [Eclipse](http://www.eclipse.org/downloads/), the "Eclipse IDE for Java Developers" is usually sufficient.
  * [Android SDK and Eclipse ADT Plugin](http://developer.android.com/sdk/installing.html), simply follow the installation instructions. If you work from source, make sure to install *all* platforms in the Android SDK manager!
  * [Google Web Toolkit](https://developers.google.com/web-toolkit/usingeclipse)

### To additionally target iOS

  * A Mac, iOS Development does not work on Windows/Linux thanks to Apple.
  * The latest XCode, which you can get from the Mac OS X App Store for free
  * [RoboVM](http://www.robovm.org/docs.html#eclipse), simply install the Eclipse plugin. Make sure to update the plugin frequently!

Once all of these tools are installed, proceed to [Project Setup](https://github.com/libgdx/libgdx/wiki/Project-setup%2C-running-%26-debugging)

### To work directly from the [source repository](https://github.com/libgdx/libgdx)
  * A Git client to fetch the source from the repository.
  * [Ant](http://ant.apache.org/bindownload.cgi). Download and extract the zip, and make sure you can execute `ant` from the command line by adding its bin/ folder to your PATH environment variable.
  * `javac` needs to be in your PATH
  * `JAVA_HOME` needs to be set to your JDK installation directory.  
  * If you want to also compile the native C/C++ sources, you will need compilers for each platform. See [[Building libgdx from source]] for more info.