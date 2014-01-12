LibGDX includes some classes for cross-platform network operations. These classes are more commonly known as Gdx.net [(docs)](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/Net.html) [(source)](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/Net.java)

# Features

* Cross-platform HTTP requests
* Multi-platform TCP client and server socket support (excludes GWT) with configurable settings
* Optimized  TCP client and server settings aiming for low-latency
* Cross-platform browser access. (ex: You can create a link to your website in game and it will open the browser on all platforms. (excludes: Headless, Android Daydreams, and Android Live Wallpapers)

## Implementation

### Notes
There are various notes needed when working with networking on different platforms.

1. TCP client and server sockets do **not** work on GWT. This is due to java.net not being supported on GWT and there is not a viable alternative at this point other than websockets.
2. Opening the browser is not supported on the headless backend, Android Daydreams, or Android Live Wallpapers. This is do the limitations with the implementation.
3. On Android: You must have the following permission declared in the AndroidManifest.xml file to access the network: `<uses-permission android:name="android.permission.INTERNET" /> `
4. On Android: You **cannot** access the network on the main thread without disabling strict mode. This is done to prevent network operations from hanging the main thread. See [here](http://developer.android.com/reference/android/os/StrictMode.html)
5. When targeting mobile devices: Be careful about how you implement networking. The wireless radios themselves are a big power drain when on. Also be careful about data limits that could be imposed on a 1G/2G/3G/4G LTE network. LibGDX has configuration optimizations done to allow low-latency, but still have the benefits of TCP.
6. Supported networking configurations vary between backend and java implementation.
7. Battery drain is more common when data is being sent and received due to the power needed by the radios.

**See Also**

Great articles about mobile data battery efficiency [here](https://developer.android.com/training/efficient-downloads/index.html)

Source code for Gdx.net classes [here](https://github.com/libgdx/libgdx/tree/master/gdx/src/com/badlogic/gdx/net)