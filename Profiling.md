This article describes the little helpers and utilities that might come in handy in case you are running into performance problems and need to start profiling your game.

FPSLogger
---------
The [`FPSLogger`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/FPSLogger.html) is a simple helper class to log the frames per seconds achieved. Just invoke the `log()` method in your rendering method. The output will be logged once per second.

PerformanceCounter
------------------
[`PerformanceCounter`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/utils/PerformanceCounter.html)

OpenGL profiling
----------------
Profiling the actual OpenGL calls that happen while your game is running is often not very easy to do, since libGDX tries to abstract all those low-level things away. In order enable the collection of these information, there is the [`GLProfiler`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/profiling/GLProfiler.html).

To enable it you have to replace the original `GL20` and `GL30` instances with the profilers, like this:

    Gdx.gl = new GL20Profiler(Gdx.gl);
    Gdx.gl20 = new GL20Profiler(Gdx.gl20);
    Gdx.gl30 = new GL30Profiler(Gdx.gl30);

Now those will be active and start to monitor the actualy GL calls for you. One information you might be interested in, could be the amount of texture bindings that happen, which are costly and might slow down your game. To optimize this, you might start to use a `TextureAtlas`. To prove with actual numbers that the texture bindings become less, you can read the `textureBinds` field from the profilers. That could look like this:

    ((GLProfiler)Gdx.gl).textureBinds + ((GLProfiler)Gdx.gl20).textureBinds + ((GLProfiler)Gdx.gl30).textureBinds)

Note that you need to add up all calls to all existing GL instances.

In order to reset those numbers once you have read and displayed them (probably once per frame), you have to call the `GLProfiler.reset()` method, like this:

    ((GL20Profiler)Gdx.gl).reset();
    ((GL20Profiler)Gdx.gl20).reset();
    ((GL30Profiler)Gdx.gl30).reset();

Note that in case you are using `Gdx.graphics.getGL20()` or `Gdx.graphics.getGL30()` you are bypassing the profiler and that's why you should use `Gdx.gl20` or `Gdx.gl30` directly.

To see how to use this you can have a look at the [Benchmark3DTest](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/g3d/Benchmark3DTest.java)