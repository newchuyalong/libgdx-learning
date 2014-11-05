LibGDX is mainly a Java-based framework. However, because Java produces Java bytecodes, and the virtual machine runs these bytecodes, it is possible to run LibGDX in any JVM language with proper Java interoperability.

## Interoperability ##

* [Ceylon](http://ceylon-lang.org/documentation/1.1/tour/interop/)
* [Clojure](http://clojure.org/java_interop)
* [Kotlin](http://confluence.jetbrains.com/display/Kotlin/Java+interoperability)
* [Python](http://www.jython.org/jythonbook/en/1.0/JythonAndJavaIntegration.html)
* [Ruby](https://github.com/jruby/jruby/wiki/CallingJavaFromJRuby)
* [Scala](http://www.scala-lang.org/old/faq/4)

## Desktop ##

This works out of the box, as the desktop LibGDX back-end uses the JVM that you have installed on your computer, which is most likely either OpenJDK or Oracle JDK. Both of these JVMs support polyglot code, as they run on .class files, not Java source code.

## Android ##

This works for many languages, but it is sometimes unavailable. For best results, search on your favorite search engine [JVM language of choice] on Android. 

Some examples: 

* [Lein-droid for Clojure](https://github.com/clojure-android/lein-droid/wiki/Tutorial)
* [SBT-Android for Scala](http://fxthomas.github.io/android-plugin/)
* [Kotlin on android using the Kotlin plugin of IntelliJ IDEA](http://blog.jetbrains.com/kotlin/2013/08/working-with-kotlin-in-android-studio/)

## iOS-ROBOVM ##

The ROBOVM backend is a JVM on iOS which executes Java bytecode. This should work, but has not been tested!

## GWT/HTML ##

GWT [transpiles](http://en.wikipedia.org/wiki/Source-to-source_compiler) Java to JavaScript. As opposed to Java bytecodes (.class files) to JavaScript code. There are a few reasons, quickly outlined by a Google employee [here](https://groups.google.com/d/msg/google-web-toolkit/SIUZRZyvEPg/OaCGAfNAzzEJ).

## Examples ##

Many people have used LibGDX in their JVM language of choice. Here are some examples.

* [Scala](https://github.com/ajhager/libgdx-sbt-project.g8) 
* [Ruby](https://github.com/kabbotta/LibGDX-and-Ruby)

TODO (find some recent examples, would love contributions!)


## reference ##

http://www.badlogicgames.com/wordpress/?p=2750