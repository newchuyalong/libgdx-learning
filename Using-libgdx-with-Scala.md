**Note: The method listed here does not support Libgdx add-ons such as Box2D, HTML5 Play and such. The compile time is also greatly increased and dealing with android is extremely painful. For these reasons using scala with Libgdx is not recommended.**

Scala is a functional, object-oriented programming language for the JVM that works seamlessly with Java libraries, frameworks, and tools. It has a concise syntax and a REPL, which makes it feel like a scripting language, but it is being used in mission critical server software at companies like Twitter and LinkedIn.

Although Scala and Java code can be freely mixed, the standard tooling for working with Scala is quite different than what Java developers will be used to. There is a project, [libgdx-sbt-project](https://github.com/ajhager/libgdx-sbt-project.g8), that provides a simple path for getting started with libgdx and Scala using standard build tools and best practices.

This tutorial assumes you have installed [g8](http://github.com/n8han/giter8) and [sbt](https://github.com/sbt/sbt) 0.12, which are used in the Scala community for generating and interacting with projects.

## Setting up a new project

In your favourite shell type:

    $ g8 ajhager/libgdx-sbt-project.g8

After filling in some information about your project, you can start placing your game's source files and assets in common/src/main/scala and common/src/main/resources, respectively.

## Managing your project

Update to the latest libraries:

    $ sbt
    > update 

Run the desktop project:

    > desktop/run

Package the desktop project into single jar:

    > assembly

Run the android project on a device:
  
    > android/start

Visit [android-plugin](https://github.com/jberkel/android-plugin) for a more in-depth guide to android configuration and usage.

Run the ios project on a device:

    > ios/device

Visit [sbt-robovm](https://github.com/ajhager/sbt-robovm) for a more in-depth guide to ios configuration and usage.

## Using unit tests

Run all unit tests from desktop, android and common (subdirectories src/test/scala):

    > test

Run specific set of unit tests:

    > common/test

## Using with popular IDEs

In most cases you will be able to open and edit each sub-project (like common, android or desktop), but you still need to use SBT to build the project.

See [here](https://github.com/ajhager/libgdx-sbt-project.g8/wiki/IDE-Plugins) for details about sbt plugins for each editor.

## Other resources
[Develop Games in Scala with libgdx](http://raintomorrow.cc/post/70000607238/develop-games-in-scala-with-libgdx-getting-started)
