If you encounter a problem when using the Bullet Wrapper, it can be sometimes difficult to find the cause of the problem. For example, you might see an error like:
```
#
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