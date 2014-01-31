### What is Gradle?
[Gradle](http://www.gradle.org/) is a dependency management and build system. 

A dependency management system is an easy way to pull in 3rd party libraries into your project, without having to store the libraries in your source tree. Instead, the dependency management system relies on a file in your source tree that specifies the names and versions of the libraries you need to be included in your application. Adding, removing and changing the version of a 3rd party library is as easy as changing a few lines in that configuration file. The dependency management system will pull in the libraries you specified from a central repository (in our case [Maven Central](http://search.maven.org/)) and store them in a directory outside of your project.

A build system helps with building and packaging your application, without being tied to a specific IDE. This is especially useful if you use a build or continuous integration server, where IDEs aren't readily available. Instead, the build server can call the build system, providing it with a build configuration so it knows how to build your application for different platforms.

In case of Gradle, both dependency management and build system go hand in hand. Both are configured in the same set of files. See the "Dependency Management" and "Packaging" sections below for more information.

### Creating a libgdx Gradle project
Libgdx comes with a file called `gdx-setup.jar` which is an executable command line tool. It takes the following arguments:

* **dir**: the directory to write the project to, relative or absolute
* **name**: the name of the application, lower-case with minuses is usually a good idea, e.g. mygame
* **package**: the Java package under which your code will live, e.g. com.badlogic.mygame
* **mainClass**: the name of the main ApplicationListener of your app, e.g. MyGame

Putting it all together, you can run the project generator on the command line as follows:

`java -jar gdx-setup.jar --dir mygame --name mygame --package com.badlogic.mygame --mainClass MyGame`

This will create a directory called `mygame`with the following layout



### Importing to Eclipse
#### As a file system
#### Using the gradle plugin
### Running & Debugging in Eclipse

### Importing to Intellij Idea
Intellij has gradle support bundled, so you can import a gradle project without installing any additional plugins.

To do this: File > Import Project, locate and choose the build.gradle in the root directory of the project and press ok. 

![Import Project](http://i.imgur.com/dx4ZJQh.png)

Keep the settings as default unless you know that you want to use a customized gradle wrapper or your local distribution. Hit OK, and it will start the import of the project and pulling in the default dependencies. 

#### A note on Intellij content root problems
Gradle in Intellij does not like any source paths that are outside of the content root. For example setting the resource directory for the desktop project as the android assets folder, this will cause a gradle refresh to fail. There are currently on a few solutions for this problem, and none of them are that ideal.  The developers have said that it will someday play nice, but as for now you can do the following:

* Dont use gradle tasks to run tasks that would require the content out of the root and keep the asset directories as 'wrong'
* Copy assets to each project
* Don't use the gradle IDE integration, run from command line and import as a file system

### Running & Debugging in Intellij Idea

### Running from the command line
The ANDROID_HOME environment variable needs to be pointing to a valid android SDK before you do any command line wizardry. To do this on Windows, you can issue this from the command line:

`set ANDROID_HOME=C:/Path/To/Your/Android/Sdk`

On Linux and Mac OS X you can do this from the shell:

`export ANDROID_HOME=/Path/To/Your/Android/Sdk`

Once you have the ANDROID_HOME environment variable pointing at the Android SDK, you can use the following to run gradle tasks from the command line:

On Windows invoke gradle like this:
`gradlew clean`

On Mac OS or Linux invoke gradle like this: 
`./gradlew clean`

Note the leading dot slash on Unix like systems. In both cases, the clean task will remove all build files from all modules in the project, e.g. class files previously generated.
#### Running the desktop project
To run the desktop project, use this gradle command:
`./gradlew desktop:run`
#### Running the android project
To run the android project, use this gradle command:
`./gradlew android:installDebug`

This task will compile the APK for android and install it on a connected device. You will have to start the app manually on the device yourself.
#### Running the gwt project
To run the gwt project, use this gradle command:
`./gradlew gwt:gwtDev`

#### Running the ios project
To run the ios project, use this gradle command:
`./gradlew ios:whoknows`


### Packaging from the command line