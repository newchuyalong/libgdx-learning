[Kotlin](https://kotlinlang.org) is a modern statically typed JVM language from [JetBrains](https://www.jetbrains.com), the creators of [IntelliJ IDEA](https://www.jetbrains.com/idea/) (Kotlin supports Eclipse too). If you’re a C# user or appreciate its features, you will feel more at home as Kotlin has many features C# has, as well as features C# can never have (null safety).

*Due to how GWT works, you will not be able to use the HTML5 target with Kotlin. This could be fixed in the future by using Kotlin’s JavaScript back-end.* It might be possible to utilize [TeaVM](https://github.com/konsoletyper/teavm) as a replacement for GWT, though.

# About the Kotlin language

Notable features:

* Null-safe types (more compile-time errors instead of always runtime ones)
* Higher-order functions
* Lambdas that work well (closures, which Java doesn’t really have)
* Cleaner syntax than Java (semi-colons are optional; `new` keyword isn't there, because it’s unnecessary)
* Extension functions (like C# has), so you can extend with static methods and create e.g. `"a string".myCustomFunction()`
* String interpolation: `println("size is ${list.size} out of $maxElements")`
* Operator overloading
* Target Java 6 transparently, without the same loss of features like you get in Java. This makes it especially attractive for Android.
* 100% interoperable with your Java libraries, and even other Java source files in your project. Seamlessly. Has a button to convert existing Java code to Kotlin too.
* Properties
* Ranges and range operator: `if (x in 0..10) println("in range!")`
* … many more

It also does not force much of anything upon you like some other languages. That is, you can create Kotlin code that is much like the same Java code (without lambdas, no higher order functions, same class/OOP design, etc). It’s a more pragmatic language, rather than academic/forceful.

See the [Kotlin Language Reference Docs](https://kotlinlang.org/docs/reference/) for deciding on and learning the language. You can also read the [Kotlin comparison to Java](https://kotlinlang.org/docs/reference/comparison-to-java.html).

# Migrating an existing project to Kotlin

This guide describes how to migrate an existing libGDX project to Kotlin. You can also start with a [fresh application](https://github.com/libgdx/libgdx/wiki/Project-Setup-Gradle).

* [Configure Gradle](#configure-gradle)
  * [Setup the Kotlin Gradle plugin](#set-up-the-kotlin-gradle-plugin)
  * [Apply the Kotlin Gradle Plugin](#apply-the-kotlin-gradle-plugin)
  * [Configuring Dependencies](#configuring-dependencies)
* [Convert Your Code From Java to Kotlin](#convert-your-code-from-java-to-kotlin)
* [Build and Run](#build-and-run)
* [Examples of libGDX projects using Kotlin](#examples-of-libgdx-projects-using-kotlin)

## Configure Gradle

This step basically includes following the [instructions from the official Kotlin manual](https://kotlinlang.org/docs/reference/using-gradle.html).

### Set up the kotlin-gradle plugin

Add the following to your parent project’s `build.gradle`:

    buildscript {
        ext.kotlinVersion = '<version to use>'

        dependencies {
            classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlinVersion"
        }
    }

Since most likely you already got the `buildscript` block in your Gradle config, make sure to only add the sub-items accordingly.

### Apply the kotlin-gradle plugin

Replace all occurrences of `apply plugin: "java"` with `apply plugin: "kotlin"`. Check your parent project's `build.gradle` as well as your sub-projects (core, desktop, ios, android).

In the android sub-project, add `apply plugin: "kotlin-android"` after the `apply plugin: "android"` line.

### Configuring Dependencies

Add Kotlin’s stdlib to your core project's dependencies list:

    dependencies {
        compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlinVersion"
    }

If you intend to use Kotlin’s reflection capabilities as well, add the respective library too:

    dependencies {
        compile "org.jetbrains.kotlin:kotlin-reflect:$kotlinVersion"
    }

## Convert your code from Java to Kotlin

You do not need to migrate all or any of your Java code right away. Both languages are fully interoperable with each other.

However, if you decide to migrate your Java code to Kotlin, IntelliJ IDEA has a handy function for that.

Open any Java file, e.g. your `DesktopLauncher` and select *Code → Convert Java File to Kotlin File* from the menu. Repeat this process for every file you want to migrate. While it is still in its infancy so it won't be error proof, but it will help you come up with more idiomatic ways of coding your project. There will be some errors, specifically with some Class<> usage, and Java code that it could not deduce null safety (because that information is lacking from Java).

## Build and run

That’s it. You successfully enabled Kotlin in your libGDX application. Build and run your project to verify that everything works.

# Examples of libGDX projects using Kotlin

These are some examples of projects that are using Kotlin, to help give you ideas on how to structure, take advantage of language features, as well as simple stuff such as build system.

* [Ore Infinium](https://github.com/sreich/ore-infinium) (desktop, moderate size, MIT license. lots of helper/wrapper code to make use of libGDX, artemis-odb, kryonet easier and more idiomatic)

*TODO:* add more real life project examples of Kotlin being used for libGDX projects.