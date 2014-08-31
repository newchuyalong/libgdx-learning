If you encounter a problem when using the Bullet Wrapper, it can be sometimes difficult to find the cause of the problem. For example, you might see an error like:
```
# A fatal error has been detected by the Java Runtime Environment:
#
#  EXCEPTION_ACCESS_VIOLATION (0xc0000005) at pc=0x000000006a49a450, pid=4040, tid=3912
#
# JRE version: Java(TM) SE Runtime Environment (8.0_05-b13) (build 1.8.0_05-b13)
# Java VM: Java HotSpot(TM) 64-Bit Server VM (25.5-b02 mixed mode windows-amd64 compressed oops)
# Problematic frame:
# C  [gdx-bullet64.dll+0x19a450]
#
# Failed to write core dump. Minidumps are not enabled by default on client versions of Windows
#
# An error report file with more information is saved as:
# C:\Xoppa\code\libgdx\tests\gdx-tests-lwjgl\hs_err_pid4040.log
#
# If you would like to submit a bug report, please visit:
#   http://bugreport.sun.com/bugreport/crash.jsp
# The crash happened outside the Java Virtual Machine in native code.
# See problematic frame for where to report the bug.
#
AL lib: (EE) alc_cleanup: 1 device not closed
```
If you look at the log file it generated, then you will see that it contains a bit more information. E.g. the stack trace of the java call. But it will not provide you much useful information about what actually went wrong inside the bullet wrapper. This is because the wrapper delegates the java calls to the native (C++) library. By default this library (e.g. on windows this would be a `.dll` file) doesn't contain any debug information.

It is possible to compile the Bullet Wrapper with debug information and even trace into the C++ wrapper and bullet code. For most problems, however, this is not necessary. The cause of most common problems can be found by inspecting the java code.

> The most common problem with the Bullet wrapper is caused by not properly maintaining references when you actually still need it. This will cause the garbage collector to destroy the native (C++) object and you application to crash. Because you can't control the garbage collector, this problem might appear more frequently on different devices. See the [creating and destroying objects](https://github.com/libgdx/libgdx/wiki/Bullet%20Wrapper:%20Using%20the%20wrapper#creating-and-destroying-objects) section for more information.

Debugging on Windows
-----
To debug the native C++ code it is necessary to build the bullet.dll with debug information.

### Getting the sources ###
To do this we will need the source code of the bullet wrapper. So head to the [libgdx github page](https://github.com/libgdx/libgdx) and either download the whole repository as a .zip file, or clone it via git (`git clone https://github.com/libgdx/libgdx.git`).

### Getting the compiler/IDE ###
Furthermore we need an IDE for the debugging as well as the compiler to build the dll. We get both these things with *Microsoft Visual Studio Express 2013*. An .iso file with the installation can be downloaded [here](http://www.microsoft.com/en-US/download/details.aspx?id=40787). After downloading it, to install it you will either need to burn the image on a CD or mount it via a virtual CD drive (for example with Daemon Tools). *Make sure that you have at least the VS Express 2013 version, since with older versions the following steps won't work.*

### Building the debug .dll ###
This is really easy as you will find a Visual Studio Project (.sln "Solution") in the sources which should be able to work out of the box. You can find it in the libgdx repository at `libgdx\extensions\gdx-bullet\jni\vs\gdxBullet\gdxBullet.sln`.

After opening the solution with Visual Studio there should 6 projects visible. The most important one is `gdxBullet`. In the toolbar at the top you need to select the correct build configurations. Make sure to select `Debug` and either `Win32` or `x64`. The platform does not depend on your Windows version, but on the version of the JVM which you are going to use to run your application. On a 64bit Windows system it is still possible (and quite common) to run a 32bit Java version.

Now right-click the `gdxBullet` project in the solution exporer and hit "Build". This will build the `gdxBullet.dll`, which might take a few minutes.

### Loading the correct DLL ###
Usually to load the natives to run bullet it is necessary to call `Bullet.init()`, which will do exactly that. Since we do not want to load the default natives without debug information included, we have to manually load our newly created `gdxBullet.dll` ourselves. You can use the following code snipped to do so. Just replace the `customDesktopLib` string with the actual path. The static block will make sure that it is loaded exactly once, when the class it is in is loaded.

	// Set this to the path of the lib to use it on desktop instead of the default lib.
	private final static String customDesktopLib = "C:\\......\\libgdx\\extensions\\gdx-bullet\\jni\\vs\\gdxBullet\\x64\\Debug\\gdxBullet.dll";
	private final static boolean debugBullet = true;

	static {
		// Need to initialize bullet before using it.
		if (Gdx.app.getType() == ApplicationType.Desktop && debugBullet) {
			System.load(customDesktopLib);
		} else {
			Bullet.init();
		}
		Gdx.app.log("Bullet", "Version = " + LinearMath.btGetVersion());
	}