Libgdx is mainly a java-based framework. However, because java produces Java Bytecodes, and the virtual machine runs these bytecodes, it is possible to run libgdx in any JVM language with proper java interoperability.

## Interoperability ##
* [Clojure](http://clojure.org/java_interop)
* [Scala](http://www.scala-lang.org/old/faq/4)
* [Kotlin](http://confluence.jetbrains.com/display/Kotlin/Java+interoperability)
* [JRuby](https://github.com/jruby/jruby/wiki/CallingJavaFromJRuby)
* [Jython](http://www.jython.org/jythonbook/en/1.0/JythonAndJavaIntegration.html)

## Desktop ##

This works out of the box, as the desktop libgdx backend uses the JVM that you have installed on your computer, which is most likely either OpenJDK or Oracle JDK. Both of these JVMs support polyglot code, as they run on .class files, not java source code.

## Android ##

This works for many languages, but it is sometimes unavailable. For best results, search on your favorite search engine [JVM language of choice] on android. 

Some examples: 

* [Lein-droid for Clojure](https://github.com/clojure-android/lein-droid/wiki/Tutorial)
* [SBT-Android for Scala](http://fxthomas.github.io/android-plugin/)
* [Kotlin on android using the Kotlin plugin of IntelliJ IDEA](http://blog.jetbrains.com/kotlin/2013/08/working-with-kotlin-in-android-studio/)

## iOS-ROBOVM ##

The ROBOVM backend is a JVM on iOS which executes java bytecode. This should work, but has not been tested!

## GWT/HTML ##

GWT [transpiles](http://en.wikipedia.org/wiki/Source-to-source_compiler) Java to JavaScript. As opposed to Java Bytecodes (.class files) to JavaScript code. There are a few reasons, quickly outlined by a Google Employee [here](https://groups.google.com/d/msg/google-web-toolkit/SIUZRZyvEPg/OaCGAfNAzzEJ).

## Examples ##

Many people have used libgdx in their JVM language of choice. Here are some examples.

* [Scala](https://github.com/ajhager/libgdx-sbt-project.g8) 

TODO (find some recent examples, would love contributions!)


## reference ##
http://www.badlogicgames.com/wordpress/?p=2750