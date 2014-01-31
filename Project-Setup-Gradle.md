### What is Gradle?
Gradle is a dependency management and build system. 

A dependency management system is an easy way to pull in 3rd party libraries into your project, without having to store the libraries in your source tree. Instead, the dependency management system relies on a file in your source tree that specifies the names and versions of the libraries you need to be included in your application. Adding, removing and changing the version of a 3rd party library is as easy as changing a few lines in that configuration file. The dependency management system will pull in the libraries you specified from a central repository (in our case Maven Central) and store them in a directory outside of your project.

A build system helps with building and packaging your application, using very simple commands. This is especially useful if you use a build or continuous integration server, where you usually can't fire up your IDE of choice to export an application package like a JAR, APK or Bundle. Instead, the build server can call the build system, providing it with a build configuration so it knows which parts of your source repository belong to what.

In case of Gradle, both dependency management and build system go hand in hand. Both are configured in the same set of files. See the "Dependency Management" and "Packaging" sections below for more information.

### Creating a libgdx Gradle project

### Importing to Eclipse

### Running & Debugging in Eclipse

### Importing to Intellij Idea

### Running & Debugging in Intellij Idea

### Running from the command line

### Packaging from the command line