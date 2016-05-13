[Kotlin](https://kotlinlang.org) is a modern statically typed JVM language from [JetBrains](https://www.jetbrains.com), the creators of [IntelliJ IDEA](https://www.jetbrains.com/idea/).

This guide describes how to migrate an existing LibGDX project to Kotlin. You can also start with a [fresh application](https://github.com/libgdx/libgdx/wiki/Project-Setup-Gradle).

# Configure Gradle

This step basically includes following the [instructions from the official Kotlin manual](https://kotlinlang.org/docs/reference/using-gradle.html).

## Set up the kotlin-gradle plugin

Add the following to your parent project's `build.gradle`:

    buildscript {
        ext.kotlinVersion = '<version to use>'

        dependencies {
            classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlinVersion"
        }
    }

Since most likely you already got the `buildscript` block in your Gradle config, make sure to only add the sub-items accordingly.

## Apply the kotlin-gradle plugin

Replace all occurrences of `apply plugin: "java"` with `apply plugin: "kotlin"`. Check your parent project's `build.gradle` as well as your sub-projects (core, desktop, ios, android).

In the android sub-project, add `apply plugin: "kotlin-android"` after the `apply plugin: "android"` line.

## Configuring Dependencies

Add Kotlin's stdlib to your core project's dependencies list:

    dependencies {
        compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlinVersion"
    }

If you intend to use Kotlin's reflection capabilities as well, add the respective library too:

    dependencies {
        compile "org.jetbrains.kotlin:kotlin-reflect:$kotlinVersion"
    }

# Convert your code from Java to Kotlin

You do not need to migrate all or any of your Java code right away. Both languages are fully interoperable with each other.

However, if you decide to migrate your Java code to Kotlin, IntelliJ IDEA has a handy function for that.

Open any Java file, e.g. your `DesktopLauncher` and select _Code -> Convert Java File to Kotlin File_ from the menu. Repeat this process for every file you want to migrate.

# Build and run

That's it. You successfully enabled Kotlin in your LibGDX application. Build and run your project to verify that everything works.