### What is Gradle?
[Gradle](http://www.gradle.org/) is a dependency management and build system. 

A dependency management system is an easy way to pull in 3rd party libraries into your project, without having to store the libraries in your source tree. Instead, the dependency management system relies on a file in your source tree that specifies the names and versions of the libraries you need to be included in your application. Adding, removing and changing the version of a 3rd party library is as easy as changing a few lines in that configuration file. The dependency management system will pull in the libraries you specified from a central repository (in our case [Maven Central](http://search.maven.org/)) and store them in a directory outside of your project.

A build system helps with building and packaging your application, without being tied to a specific IDE. This is especially useful if you use a build or continuous integration server, where IDEs aren't readily available. Instead, the build server can call the build system, providing it with a build configuration so it knows how to build your application for different platforms.

In case of Gradle, both dependency management and build system go hand in hand. Both are configured in the same set of files. See the "Dependency Management" and "Packaging" sections below for more information.

### Creating a libgdx Gradle project

### Importing to Eclipse

### Running & Debugging in Eclipse

### Importing to Intellij Idea

### Running & Debugging in Intellij Idea

### Running from the command line

### Running from the command line
Once you have the ANDROID_HOME environment variable pointing at the Android SDK, you can use the following to run gradle tasks:


On Windows:

`gradlew clean`

On Mac IS or linux 

`./gradlew clean`
#### Running the desktop project
`./gradlew desktop:run`
#### Running the android project
`./gradlew android:installDebug`
#### Running the gwt project
`./gradlew gwt:gwtDev`
#### Running the ios project
`./gradlew ios:whoknows`


### Packaging from the command line