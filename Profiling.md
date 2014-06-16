This article describes the little helpers and utilities that might come in handy in case you are running into performance problems and need to start profiling your game.

FPSLogger
=========
The [`FPSLogger`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/FPSLogger.html) is a simple helper class to log the frames per seconds achieved. Just invoke the `log()` method in your rendering method. The output will be logged once per second.

PerformanceCounter
==================
[`PerformanceCounter`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/utils/PerformanceCounter.html)

OpenGL profiling
================
[`GLProfiler`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/profiling/GLProfiler.html)

Note that in case you are using `Gdx.graphics.getGL20()` or `Gdx.graphics.getGL30()` you are bypassing the profiler and that's why you should use `Gdx.gl20` or `Gdx.gl30` directly.

To see how to use this you can have a look at the [Benchmark3DTest](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/g3d/Benchmark3DTest.java)