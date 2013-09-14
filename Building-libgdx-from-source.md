Before trying to build libgdx from source yourself, make sure you have the [[Prerequisites]] installed!

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

  * C/C++ code can only be accessed from within Java via [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface), for which we have a special solution called [gdx-jnigen](http://www.badlogicgames.com/wordpress/?p=2254).
  * We need to build for multiple platforms, Windows (32/64bit), Linux (32/64-bit), Mac OS X (32/64bit), Android(arm6/arm7) and iOS(i386/arm7)
  * The Mac OS X and iOS builds can only be executed on a Mac, insert mandatory Apple hate here.
  * In addition to compiling the C/C++ natives, we also need to convert various jar files to .Net assemblies via IKVM for iOS.

Let's discuss each individual part, before diving into setting up the necessary cross-compilation tool-chains and looking at the compilation flow.

### Jnigen ###
[Jnigen](https://github.com/libgdx/libgdx/tree/master/extensions/gdx-jnigen) is an extension that lets you put C/C++ code directly into your Java source code. This increases locality of code that conceptually belongs together (the native Java class methods and the actual implementation) and makes refactoring a lot easier as compared to the general JNI workflow.

jnigen has two functions:
  
  * Inspect Java source files in a specific folder, detect native methods and the attached C++ implementation, and spit out a C++ source file and header, similar to what you'd create manually with JNI.
  * Provide a generator for Ant build scripts that build the native source for every platform.


### Native Code Generation ###
Here's an example of Java/C++ mixed in a single Java source file as understood by jnigen (taken from [BufferUtils](https://github.com/libgdx/libgdx/blob/master/backends/gdx-backends-gwt/src/com/badlogic/gdx/backends/gwt/emu/com/badlogic/gdx/utils/BufferUtils.java):


```java
private static native ByteBuffer newDisposableByteBuffer (int numBytes); /*
   char* ptr = (char*)malloc(numBytes);
   return env->NewDirectByteBuffer(ptr, numBytes);
*/

private native static void copyJni (float[] src, Buffer dst, int numFloats, int offset); /*
   memcpy(dst, src + offset, numFloats << 2 );
*/
```

The C++ code is contained in a block comment after the Java native method declaration. Java side input parameters will be available to the C++ code by their Java names, and, if possible marshaled for a limited subset of types. The following marshaling takes place:

  * Primitive types are passed as is, using jint, jshort, jboolean as their types.
  * One dimensional primitive type arrays are converted to typed pointers you can directly access. The arrays are automatically locked and unlocked for you via JNIEnv::GetPrimitiveArrayCritical and JNIEnv::ReleasePrimitiveArrayCritical.
  * Direct buffers are converted to unsigned char`*` pointers via JNIEnv::GetDirectBufferAddress. Note that the position of the buffer is not taken into account!
  * Any other type will be passed with its respective JNI type, e.g. normal Java objects will be passed as jobject.

The above two jnigen native methods would translate to the following C++ source (there would be a corresponding header file as well):

```cpp
JNIEXPORT jobject JNICALL Java_com_badlogic_gdx_utils_BufferUtils_newDisposableByteBuffer(JNIEnv* env, jclass clazz, jint numBytes) {
//@line:334
   char* ptr = (char*)malloc(numBytes);
   return env->NewDirectByteBuffer(ptr, numBytes);
}

JNIEXPORT void JNICALL Java_com_badlogic_gdx_utils_BufferUtils_copyJni___3FLjava_nio_Buffer_2II(JNIEnv* env, jclass clazz, jfloatArray obj_src, jobject obj_dst, jint numFloats, jint offset) {
   unsigned char* dst = (unsigned char*)env->GetDirectBufferAddress(obj_dst);
   float* src = (float*)env->GetPrimitiveArrayCritical(obj_src, 0);


//@line:348
   memcpy(dst, src + offset, numFloats << 2 );

   env->ReleasePrimitiveArrayCritical(obj_src, src, 0);
}
```

As you can see the marshaling is inserted at the top and bottom of the method automatically in `copyJni()`. If you return from your JNI method in place other than the end of your method, jnigen will wrap your function with a second function that does all the marshaling, like here: [Java source](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-freetype/src/com/badlogic/gdx/graphics/g2d/freetype/FreeType.java#L584) [C++ translation](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-freetype/jni/com.badlogic.gdx.graphics.g2d.freetype.FreeType.cpp#L472).

Jnigen also outputs the line numbers, telling us where in the original Java source file the C++ appeared. This is helpful if we build jnigen generated C++ code, as the Ant script will spit out errors with Java line numbers to which we can jump to by clicking on the line in the console.

To let Jnigen go through your source code and generated C/C++ header and source files, you do the following:

```java
new NativeCodeGenerator().generate("src", "bin", "jni", new String[] {"**/*"}, null);
```

You specify the source folder, the folder containing the compiled .class files of your Java classes, the Java files to include (using Ant path patterns) and the files you want to exclude. See the source of [NativeCodeGenerator](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-jnigen/src/com/badlogic/gdx/jnigen/NativeCodeGenerator.java) for more info.

#### Build Script Generation ####
Once the native code files have been generated, we also want to create build scripts for all supported platforms. This currently includes Windows (32-/64-bit), Linux (32-/64-bit), Mac OS X (x86, 32-/64-bit), Android (arm6/arm7) and iOS (i386, arm7). The build script generator of jnigen has template Ant script files that can be parametrized for each platform. The parameters are specified via a [BuiltTarget](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-jnigen/src/com/badlogic/gdx/jnigen/BuildTarget.java). You can create a BuildTarget for a specific platform like this:

```java
BuildTarget linux32 = BuildTarget.newDefaultTarget(TargetOS.Linux, false);
```

This creates a default build target for Linux 32-bit. You can then add additional, platform specific settings to the BuildTarget. Repeat the process for other targets.

Once all targets are configured, you pull them together in a [BuildConfig](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-jnigen/src/com/badlogic/gdx/jnigen/BuildConfig.java). You specify the name of the shared/static library, e.g. "gdx" which will end up as gdx.dll on Windows, libgdx.so on Linux and Android, libgdx.dylib on Mac OS X and libgdx.a on iOS. You can also specify there the build files should be output to and so on. The easiest way of using the config looks like this:

```java
BuildConfig config = new BuildConfig("gdx");
```

One the targets and config are in place, it's time to generate the Ant scripts via the [AntScriptGenerator](https://github.com/libgdx/libgdx/blob/master/extensions/gdx-jnigen/src/com/badlogic/gdx/jnigen/AntScriptGenerator.java):

```java
new AntScriptGenerator().generate(config, linux32, linux64, windows32, windows64, macosx, android, ios)
```