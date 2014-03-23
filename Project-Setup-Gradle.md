### Creating a libgdx project
Libgdx comes with a file called `gdx-setup.jar` which is an executable UI and command line tool. You can simply execute the JAR file which will open the setup UI.

#### [Download gdx-setup.jar](http://libgdx.badlogicgames.com/nightlies/dist/gdx-setup.jar)

![setup ui](http://libgdx.badlogicgames.com/uploads/Screen%20Shot%202014-03-23%20at%2018.54.16-6c0i9ho3ym.png)

Specify your application name, your Java package name, the name of your main class and the output directory, then click "Generate". Now you are ready to import the project into your IDE, run, debug and package it!

  * [[Eclipse|Gradle and Eclipse]]
  * [[Intellij IDEA|Gradle and Intellij IDEA]]
  * [[Commandline|Gradle on the Commandline]]

### Creating a libgx project on the command line
IF you run it from the command line, specify the following arguments.

* **dir**: the directory to write the project to, relative or absolute
* **name**: the name of the application, lower-case with minuses is usually a good idea, e.g. mygame
* **package**: the Java package under which your code will live, e.g. com.badlogic.mygame
* **mainClass**: the name of the main ApplicationListener of your app, e.g. MyGame

Putting it all together, you can run the project generator on the command line as follows:

`java -jar gdx-setup.jar --dir mygame --name mygame --package com.badlogic.mygame --mainClass MyGame`

### Project layout
This will create a directory called `mygame`with the following layout:

```
settings.gradle            <- definition of sub-modules. By default core, desktop, android, gwt, ios
build.gradle               <- main Gradle build file, defines dependencies and plugins
gradlew                    <- script that will run Gradle on Unix systems
gradlew.bat                <- script that will run Gradle on Windows
gradle                     <- local gradle wrapper

core/
    build.gradle           <- Gradle build file for core project, no touchy!
    src/                   <- Source folder for all your game's code

desktop/
    build.gradle           <- Gradle build file for desktop project, no touchy!
    src/                   <- Source folder for your desktop project, contains Lwjgl launcher class

android/
    build.gradle           <- Gradle build file for android project, no touchy, seriously this time
    AndroidManifest.xml    <- Android specific config
    assets/                <- contains for your graphics, audio, etc.  Shared with other projects.
    res/                   <- contains icons for your app and other resources
    src/                   <- Source folder for your Android project, contains android launcher class

gwt/
    build.gradle           <- Gradle build file for the gwt project, no touchy
    src/                   <- Source folder for your gwt project, contains launcher and gwt definition
    webapp/                <- War template, on generation the contents are copied to war. Contains startup url index page and web.xml


ios/
    build.gradle           <- Gradle build file for the ios project, no touchy unless masochistic
    src/                   <- Source folder for your ios project, contains launcher
```

### What is Gradle?
[Gradle](http://www.gradle.org/) is a dependency management and build system. 

A dependency management system is an easy way to pull in 3rd party libraries into your project, without having to store the libraries in your source tree. Instead, the dependency management system relies on a file in your source tree that specifies the names and versions of the libraries you need to be included in your application. Adding, removing and changing the version of a 3rd party library is as easy as changing a few lines in that configuration file. The dependency management system will pull in the libraries you specified from a central repository (in our case [Maven Central](http://search.maven.org/)) and store them in a directory outside of your project.

A build system helps with building and packaging your application, without being tied to a specific IDE. This is especially useful if you use a build or continuous integration server, where IDEs aren't readily available. Instead, the build server can call the build system, providing it with a build configuration so it knows how to build your application for different platforms.

In case of Gradle, both dependency management and build system go hand in hand. Both are configured in the same set of files. See the "Dependency Management" and "Packaging" sections below for more information.