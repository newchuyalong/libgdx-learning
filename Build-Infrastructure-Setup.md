Our build infrastructure has the responsiblity of building libgdx for all supported platforms (Windows, Linux, Mac OS X, Android, iOS, HTML). On top of the boring old Java code, we also need to compile the native code for each platform and architecture (32-, 64-bit if available).

We solve this issue by using cross-compilation on a Linux host to compile native code for Windows, Linux and Android. We additionally use a Mac to compile the iOS and Mac OS X native binaries.

The following sections document how to setup this build infrastructure.

## Setting up the Linux host
The Linux host has to be a 64-bit installation. We use Ubuntu 13.10, newer releases or other Linux distributions should work just as well. The following Debian packages can be installed from the official repositories

* unzip
* daemon
* git
* openjdk-7-jdk
* jenkins (latest)
  * `wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -`
  * add `deb http://pkg.jenkins-ci.org/debian binary/` to `/etc/apt/sources.list`
  * `sudo apt-get update` followed by `sudo apt-get install jenkins`, this will install the latest Jenkins version and provide an update path in the future instead of using the Jenkins version from the Ubuntu repositories
  * after installation, open `/etc/default/jenkins` and set `HTTP_HOST` and `AJP_HOST` to `0.0.0.0` to listen for connections on all net devices
  * setup users, enable global security using the Jenkins user database, letting any logged in user to anything
  * install the following plugins: Copy to Slave Plugin, Git Plugin, Mailer, Maven Project Plugin, Parameterized Trigger Plugin, Gradle Plugin
  * Go to the "Configure System" site and set the Ant and installation locations
* gcc, g++, gcc-multilib, g++-multilib (linux compilers, 32- and 64-bit)
* mesa-common-dev, libxxf86vm-dev, libxrandr-dev (Linux OpenGL development, only needed for jglfw, 64-bit only)
* mingw-w64 (windows compilers, 32- and 64-bit)
* ccache

The following software has to be installed manually

* [Ant 1.9.3+](http://ant.apache.org/) (fixes a bug that's vital for build time reduction!)
  * Download the latest binary distribution, unzip it to /opt/ant
  * Add `export PATH=$PATH:/opt/ant/bin` to `/etc/profile`
  * Make sure the Jenkins user can execute Ant
* [Maven 3+](http://maven.apache.org/download.cgi)
  * Download the latest binary distribution, unzip it to /opt/maven
  * Add `export PATH=$PATH:/opt/mave/bin` to `/etc/profile`
  * Make sure the Jenkins user can execute Maven
  * modify /opt/maven/conf/settings.xml to include the credentials for SonaType snapshot deployment (ask Mario)
* [Android NDK](https://developer.android.com/tools/sdk/ndk/index.html)
  * Download the latest tar.bz2, unpack to /opt/android-ndk
  * Add `export ANDROID_NDK=/opt/android-ndk` to `/etc/profile`
  * Add `export NDK_HOME=/opt/android-ndk` to `/etc/profile`
  * Make sure the Jenkins user can execute ndk-build

## Setting up the Mac OS X host
We need a Mac to build natives for iOS and Mac OS X. This host will then be used as a Jenkins slave to build parts of the libgdx project. This dependency is expressed in the Jenkins Jobs responsible for building libgdx, see below. The Mac needs to have the following things installed:

* [JDK 8+](http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html)
* XCode, install via the Mac App Store
  * Make sure the command line utilities are also installed, search Google for how to do this with the latest XCode.
  * you don't need a developers license to compile for a device, you just can't deploy to one
* Ant 1.9.3, install via [Homebrew](http://brew.sh/)

Once the Mac is setup, you can move on to setting up the Jobs on Jenkins

## Setting up the libgdx Jenkins jobs
With your Linux host running Jenkins, go to http://JENKINS_ADDRESS:8080.

### Setting up the Mac Slave
* On the landing page, click on `Manage Jenkins`, then go to `Manage Nodes`. 
* Click on `New Node`, give it the name `Mac`, select `Dumb Slave` and press `OK`.
* Set the `Remote FS root` to a folder of your choice, e.g. `/Users/badlogic/jenkins`
* Set `Usage` to `Leave this machine for tied jobs only`
* Set `Launch method` to `Launch Slave agents via Java Web Start`

You can now startup your Mac, open a browser and navigate to http://JENKINS_ADDRESS:8080/computer/Mac/. Click on the `Launch` button to download the Java Web start file, double click it to run the slave. Once it's running, you should see marked as being online in your Jenkins instance.

## Setting up the libgdx Mac Job
* 