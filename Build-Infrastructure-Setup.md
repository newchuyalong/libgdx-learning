Our build infrastructure has the responsiblity of building libgdx for all supported platforms (Windows, Linux, Mac OS X, Android, iOS, HTML). On top of the boring old Java code, we also need to compile the native code for each platform and architecture (32-, 64-bit if available).

We solve this issue by using cross-compilation on a Linux host to compile native code for Windows, Linux and Android. We additionally use a Mac to compile the iOS and Mac OS X native binaries.

The following sections document how to setup this build infrastructure.

## Setting up the Linux host
The Linux host has to be a 64-bit installation. We use Ubuntu 13.10, newer releases or other Linux distributions should work just as well. The following Debian packages can be installed from the official repositories

* openjdk-7-jdk
* jenkins (at least version 1.509.2)
* git