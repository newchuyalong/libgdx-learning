### Creating a libgdx project
Libgdx comes with a file called `gdx-setup.jar` which is an executable UI and command line tool. You can simply execute the JAR file which will open the setup UI.
<br>To execute the JAR file at the command line:
<br>java -jar gdx-setup.jar

## [Download gdx-setup.jar](https://bitly.com/1i3C7i3)

Specify your application name, your Java package name, the name of your main class, the output directory, and the path to your android sdk. Next, you can select what platforms you want to support. **Note: once chosen, you'll have to add new platforms manually!**. Finally, you can select extensions to be included in your app. Some may not work on all platforms, for which you'll get a warning. When you've set everything, click "Generate". **Now you are ready to import the project into your IDE, run, debug and package it!**

  * [[Eclipse|Gradle and Eclipse]]
  * [[Intellij IDEA and Android studio|Gradle and Intellij IDEA]]
  * [[NetBeans|Gradle and NetBeans]]
  * [[Commandline|Gradle on the Commandline]]

Note that the Advanced button lets you set the project generation to generate Eclipse and/or IDEA projects **without** Gradle integration, as described in more detail in the wiki article about [workflow without Gradle](Improving-workflow-with-Gradle#how-to-remove-gradle-ide-integration-from-your-project), as well as options to use an alternative repository to Maven Central and to not force downloading dependencies.

![screen capture of gdx-setup.jar](http://i.imgur.com/mCToniB.png)


### Creating a libgdx project on the command line
IF you run it from the command line, specify the following arguments.

* **dir**: the directory to write the project to, relative or absolute
* **name**: the name of the application, lower-case with minuses is usually a good idea, e.g. mygame
* **package**: the Java package under which your code will live, e.g. com.badlogic.mygame
* **mainClass**: the name of the main ApplicationListener of your app, e.g. MyGame
* **sdkLocation**: the location of your android sdk, Intellij uses this if ANDROID_HOME is not set

Putting it all together, you can run the project generator on the command line as follows:

`java -jar gdx-setup.jar --dir mygame --name mygame --package com.badlogic.mygame --mainClass MyGame --sdkLocation mySdkLocation`

### Project layout
This will create a directory called `mygame`with the following layout:

```
settings.gradle            <- definition of sub-modules. By default core, desktop, android, html, ios
build.gradle               <- main Gradle build file, defines dependencies and plugins
gradlew                    <- script that will run Gradle on Unix systems
gradlew.bat                <- script that will run Gradle on Windows
gradle                     <- local gradle wrapper
local.properties           <- Intellij only file, defines android sdk location

core/
    build.gradle           <- Gradle build file for core project*
    src/                   <- Source folder for all your game's code

desktop/
    build.gradle           <- Gradle build file for desktop project*
    src/                   <- Source folder for your desktop project, contains Lwjgl launcher class

android/
    build.gradle           <- Gradle build file for android project*
    AndroidManifest.xml    <- Android specific config
    assets/                <- contains for your graphics, audio, etc.  Shared with other projects.
    res/                   <- contains icons for your app and other resources
    src/                   <- Source folder for your Android project, contains android launcher class

html/
    build.gradle           <- Gradle build file for the html project*
    src/                   <- Source folder for your html project, contains launcher and html definition
    webapp/                <- War template, on generation the contents are copied to war. Contains startup url index page and web.xml


ios/
    build.gradle           <- Gradle build file for the ios project*
    src/                   <- Source folder for your ios project, contains launcher
```
\* These scripts contain tasks that package natives and distribute your applications on the respective platforms, you can add/maintain these tasks yourself, but only do so if you are familiar with Gradle, and what these tasks are doing, otherwise you will break your project.

### What is Gradle?
[Gradle](http://www.gradle.org/) is a dependency management and build system. 

A dependency management system is an easy way to pull in 3rd party libraries into your project, without having to store the libraries in your source tree. Instead, the dependency management system relies on a file in your source tree that specifies the names and versions of the libraries you need to be included in your application. Adding, removing and changing the version of a 3rd party library is as easy as changing a few lines in that configuration file. The dependency management system will pull in the libraries you specified from a central repository (in our case [Maven Central](http://search.maven.org/)) and store them in a directory outside of your project.

A build system helps with building and packaging your application, without being tied to a specific IDE. This is especially useful if you use a build or continuous integration server, where IDEs aren't readily available. Instead, the build server can call the build system, providing it with a build configuration so it knows how to build your application for different platforms.

In case of Gradle, both dependency management and build system go hand in hand. Both are configured in the same set of files. See the [[Dependency management with Gradle]] and "Packaging" sections below for more information.