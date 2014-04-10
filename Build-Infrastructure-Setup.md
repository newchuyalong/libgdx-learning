Our build infrastructure has the responsiblity of building libgdx for all supported platforms (Windows, Linux, Mac OS X, Android, iOS, HTML). On top of the boring old Java code, we also need to compile the native code for each platform and architecture (32-, 64-bit if available).

We solve this issue by using cross-compilation on a Linux host to compile native code for Windows, Linux and Android. We additionally use a Mac to compile the iOS and Mac OS X native binaries.

The following sections document how to setup this build infrastructure.

## Setting up the Linux host
The Linux host has to be a 64-bit installation. We use Ubuntu 13.10, newer releases or other Linux distributions should work just as well. The following Debian packages can be installed from the official repositories

* openjdk-7-jdk
* daemon
* jenkins (at least version 1.509.2)
  * after installation, open `/etc/default/jenkins` and set `HTTP_HOST` and `AJP_HOST` to `0.0.0.0` to listen for connections on all net devices
* git
* ccache

The following software has to be installed manually

* [Ant 1.9.3+](http://ant.apache.org/) (fixes a bug that's vital for build time reduction!)
  * Download the latest binary distribution, unzip it to /opt/ant
  * Add `export PATH=$PATH:/opt/ant/bin` to `/etc/profile`
* [Android SDK](https://developer.android.com/tools/sdk/ndk/index.html)
  * Download the latest tar.bz2, unpack to /opt/android-ndk
  * Add `export ANDROID_NDK=/opt/android-ndk` to `/etc/profile`