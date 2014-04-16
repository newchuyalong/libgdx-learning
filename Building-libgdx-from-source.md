Before trying to build libgdx from source yourself, you'll have to install [[Eclipse and all other related software|Setting up your Development Environment (Eclipse, Intellij IDEA, NetBeans)]]. We currently don't support development with other IDEs.

In addition you'll have to install [Ant](http://ant.apache.org/) and make sure its `bin/` directory is in your `PATH` environment variable so that you can execute Ant on the command line.

You can work with libgdx sources without ever having to touch the native C/C++ portions. For 99% of users that's all they need. For these contributors we provide the easy way to work with libgdx. For those hardcore 1%, you'll have to dive into building not only the Java sources, but also the native sources.

## Easy Way ##
Libgdx's build system is based on [Ant](http://ant.apache.org/). Various Ant scripts are responsible for building different parts of libgdx.

The main build script is called `build.xml` and located in the root directory of the libgdx repository. It builds a full distribution of libgdx, including the core API, the back-ends, all extensions and the Javadocs. To build such a distribution, invoke the following in the shell:

```
ant -f fetch.xml
ant
```

This will first fetch the latest native libraries (compiled C/C++ code for all platforms) from the [build server](http://libgdx.badlogicgames.com/nightlies/) so you don't have to build them yourself. Next it will invoke the main build script to build all the Java parts. 

The end result is a zip file called libgdx-version.zip and a folder called `dist` containing the expanded contents of the zip file, which will essentially be the same as the one you can get from the nightly build server, plus any modifications you made to the source.

The `build.xml` file has targets for every module. Each target configures a few properties (classpath, output directory, etc.) which are then send as input to the `build-template.xml` file. The `build-template.xml` is then responsible for compiling the Java source code as well as the native source code. The later is done by invoking an Ant script called `build.xml` in the `jni/` folder of the module. If you use the above method to compile the native builds will not be executed. See below for info on how to compile the native sources.

## Hard Way ##
There are a couple of challenges involved in building libgdx's native source code:

  * C/C++ code can only be accessed from within Java via [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface).
  * We need to build for multiple platforms, Windows (32/64bit), Linux (32/64-bit), Mac OS X (32/64bit), Android(arm6/arm7) and iOS(i386/arm7)
  * The Mac OS X and iOS builds can only be executed on a Mac, insert mandatory Apple hate here.

We have a special solution called [jnigen](jnigen) for building the libgdx native libraries. You can also look at how our entire [[Build infrastructure setup]] if you want to fully reproduce all the native builds in an automated way!