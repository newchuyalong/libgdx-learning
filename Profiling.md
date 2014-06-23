This article describes the little helpers and utilities that might come in handy in case you are running into performance problems and need to start profiling your game.

FPSLogger
---------
The [`FPSLogger`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/FPSLogger.html) is a simple helper class to log the frames per seconds achieved. Just invoke the `log()` method in your rendering method. The output will be logged once per second.

PerformanceCounter
------------------
The [`PerformanceCounter`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/utils/PerformanceCounter.html) keeps track of the time and load (percentage of total time) a specific task takes. Call `start()` just before starting the task and `stop()` right after. You can do this multiple times if required. Every render or update call `tick()` to update the values. The `time` FloatCounter provides access to the minimum, maximum, average, total and current time the task takes. Likewise for the `load` value, which is the percentage of the total time.

OpenGL profiling
----------------
Profiling the actual OpenGL calls that happen while your game is running is often not very easy to do, since libGDX tries to abstract all those low-level things away. In order to enable the collection of these information, there is the [`GLProfiler`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/profiling/GLProfiler.html).

To enable it you have to call the static method `GLProfiler.enable()`. Behind the scenes this will replace the original `GL20` and `GL30` instances with the profilers.

Now those will be active and start to monitor the actual GL calls for you. One information you might be interested in, could be the amount of texture bindings that happen, which are costly and might slow down your game. To optimize this, you might start to use a `TextureAtlas`. To prove with actual numbers that the texture bindings become less, you can read the static field `GLProfiler.textureBindings` from the profiler.

You might also implement something like view frustum culling to render only those things that are visible on the screen. The static field `GLProfiler.drawCalls` will show the results of these kind of optimizations.

Currently the following informations are provided by the profiler:
- Amount of total OpenGL calls
- Amount of draw calls
- Amount of texture bindings
- Amount of shader switches
- Amount of used vertices

`GLProfiler.vertexCount` is actually a [`FloatCounter`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/math/FloatCounter.html). Besides `GLProfiler.vertexCount.total` it has more information like `GLProfiler.vertexCount.min`, `GLProfiler.vertexCount.max` or `GLProfiler.vertexCount.average`, which are the values based on individual drawcalls.

In order to reset all these numbers once you have read and displayed them (probably once per frame), you have to call the `GLProfiler.reset()` method. To completely disable the profiling and replace the profilers with the original `GL20` and `GL30` instances, use `GLProfiler.disable()`.

*Note that in case you are using `Gdx.graphics.getGL20()` or `Gdx.graphics.getGL30()` you are bypassing the profiler and that's why you should use `Gdx.gl20` or `Gdx.gl30` directly.*

To see how to use this you can have a look at the [Benchmark3DTest](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/g3d/Benchmark3DTest.java)