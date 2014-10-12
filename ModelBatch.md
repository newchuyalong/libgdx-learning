[ModelBatch](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/ModelBatch.html) ([code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/ModelBatch.java)) is a class used for managing render calls. It is typically used to render instances of [models](https://github.com/libgdx/libgdx/wiki/Models), although it is not restricted to models. The ModelBatch class abstracts away all rendering code, providing a layer on top of it and allowing you to focus on more game specific logic. Every part of the ModelBatch functionality is customizable by design.

**Caution:** because ModelBatch manages the render calls and therefore the rendering context, you should not try to manually modify the render context (e.g. bind shaders, texture or meshes, or call any function starting with `gl`) in between the `ModelBatch.begin()` and `ModelBatch.end()` calls. This will not work and might cause unpredictable behavior. Instead use the customization options that ModelBatch offers.

# Common misconceptions
* ModelBatch is often compared to [SpriteBatch](https://github.com/libgdx/libgdx/wiki/Spritebatch%2C-Textureregions%2C-and-Sprites). While this might be understandable from an API view, there are some very big differences making them less comparable. The main difference is that SpriteBatch merges multiple sprites into a single draw call, while ModelBatch doesn't combine render calls. This does have performance implications, so be aware to merge any render calls before sending them to the ModelBatch.
* ModelBatch does not perform (frustum) culling. It simply hasn't enough information to do this using the best performing method. By default, every call to `ModelBatch.render()` will at least lead to one actual render call. ModelBatch does allow you to customize this though and perform frustum culling prior to actually rendering. However, typically, you should perform (frustum) culling prior to calling `ModelBatch.render()`.

# Overview
So what does ModelBatch actually do?

1.  It gathers render calls
2.  It gathers a shader for each render call
3.  It sorts the render calls
4.  It manages the rendering context
5.  It executes the render calls

That's it. Nothing more, nothing less. And every part of this is customizable. Please be aware that because of this design, there might be multiple ways to accomplish the same basic task.

# Using ModelBatch
ModelBatch is a relatively heavy weight object, because of the shaders it might create. When possible you should try to reuse it. You'd typically create a ModelBatch in the `create()` method. Because it contains native resources (like the shaders it uses), you'll need to call the `dispose()` method when no longer needed.

Rendering should be done every frame, typically in your `render()` method. To start rendering you should call `modelBatch.begin(camera);`. Next use the `modelBatch.render(...);` method to add one or more render calls. When done adding render calls, you must call `modelBatch.end();` to actually render to specified calls.
```java
    ModelBatch modelBatch;
    ...

    @Override
    public void create () {
        modelBatch = new ModelBatch();
        ...
    }

    @Override
    public void render () {
        ... // call glClear etc.
        modelBatch.begin(camera);
        modelBatch.render(...);
        ... // add other render calls
        modelBatch.end();
        ...
    }

    @Override
    public void dispose () {
        modelBatch.dispose();
        ...
    }
```
The call to `modelBatch.render(...)` is only valid in between the call to `modelBatch.begin(camera)` and `modelBatch.end()`. The actual rendering is performed at the call to `end();`. If you want to force rendering in between, then you can use the `modelBatch.flush();` method.

The [`Camera`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/Camera.html) you supply is hold by reference, meaning that it must not be changed in between the begin and end calls. If you need to switch camera in between the begin and end calls, then you can call the `modelBatch.setCamera(camera);`, which will `flush()` the batch if needed.

# Gather render calls
## What are render calls?
The purpose of ModelBatch is to manage render calls. So what exactly is a render call and how do you specify them?

A "render call" (often also referred to as "draw call") is basically the instruction to the GPU to render (display) something. Simply said, each "render call" displays a shape with some properties (e.g. a location, image, color, etc). Or to be more precise, it instructs the GPU to render a given part of a mesh using a given shader in a given context. We will look more in depth on this later.

> For basic usage, you don't have to know the exact details about a render call, because ModelBatch abstracts them away. However, there's a performance impact of render calls. Typically each render call is executed on the GPU, meaning that it is executed in parallel to CPU code. Whenever a new render call is executed, it might imply that GPU and CPU need to be synchronized. Or in other words, having are few large render calls is typically better performing than having many smaller render calls.

To specify a render call, libGDX contains the [Renderable](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/Renderable.html) ([code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/Renderable.java)) class, which contains almost everything (except for the camera) required to perform a single render call. Basically it contains how (the shader) and where (the transformation) to render the shape (the mesh part) in which context (the environment and material).

The `render(...)` method of `ModelBatch` has many signatures (variations with different method arguments), one of which is `ModelBatch.render(Renderable);`. Using this method you can directly specify the render call you want to add to the ModelBatch. The other `render(...)` methods allow you to specify one or more render calls using a `RenderableProvider`. In those methods, the other arguments let you provide default values for those render calls. For example, when using the `ModelBatch#render(RenderableProvider, Environment, Shader)` method it will set (override) the `environment` and `shader` members of every `Renderable` the `RenderableProvider` provides.

## RenderableProvider
[`RenderableProvider`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/RenderableProvider.html) ([code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/RenderableProvider.java)) is an interface which you can implement to supply one or more `Renderable` instances. Probably the most common implementation of this interface is the [`ModelInstance`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/ModelInstance.html) class, which traverses all [[nodes | Models#nodes]] ([[parts | Models#nodepart]]) and translates them into a `Renderable` instance. However, you can use the `RenderableProvider` anyway you need. For example, when creating a voxel engine, you could create a `Renderable` for each chunk. Or when using an entity component system, you could use `RenderableProvider` as component.

`RenderableProvider` only has one method:
```java
public void getRenderables (Array<Renderable> renderables, Pool<Renderable> pool);
```
This is a very open API (e.g. it gives you access to the `Array` of all previously added `Renderable`s), but you should restrict your usage to only adding elements to the array. The pool can optionally be used to avoid allocation. You're free to ignore it or to use it for any dynamic `Renderable` needed. Any `Renderable` you `obtain()` from it, will be automatically be free'd by the `ModelBatch`, you don't have to take care for that.

# Gather shaders
## What is a shader?
A [`Shader`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/Shader.html)  ([code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/Shader.java)) is an interface that abstracts the implementation of actually performing the render call. Typically its implementation uses a [`ShaderProvider`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/glutils/ShaderProgram.html) which is the GPU program (e.g. the _vertex shader_ and _fragment shader_) needed to perform the render call. The `Shader` implementation also contains everything needed to use this `ShaderProgram`, like setting _uniform_ values.

## ShaderProvider
Independent of how the actual rendering is performed, the ModelBatch needs one `Shader` per `Renderable`. For this it uses the [`ShaderProvider`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/utils/ShaderProvider.html) ([code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/utils/ShaderProvider.java)) interface. For every `Renderable` added to the batch (even if it contains a shader), the `getShader` of the `ShaderProvider` will be called to fetch to shader to render it.

```java
public Shader getShader (Renderable renderable);
```

By default the [DefaultShaderProvider](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/utils/DefaultShaderProvider.html) ([code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/utils/DefaultShaderProvider.java)) is used, which will create a [DefaultShader](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/shaders/DefaultShader.html) whenever a previous created shader can't be reused. You can, however, customize this by supplying your own `ShaderProvider` or by extending the `DefaultShaderProvider`.

`ModelBatch` delegates managing `Shader`'s to the `ShaderProviders`. Because a `Shader` typically uses a `ShaderProgram`, they need to be disposed. When `modelBatch.dispose();` is called, `ModelBatch` will call the `dispose()` method the `ShaderProvider`.

# Sorting render calls
If render calls would be executed in a random order, then it would cause strange and less performing result. For example, if a transparent object would be rendered prior to an object that's behind it then you won't see the object behind it. This is because the depth buffer will prevent the object further away from being rendered. Sorting the render calls helps to solve this.

By default `ModelBatch` will use the [DefaultRenderableSorter](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/utils/DefaultRenderableSorter.html) ([code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/utils/DefaultRenderableSorter.java)) to sort the render calls. This implementation will cause that opaque objects are rendered first from front to back, after which transparent objects are rendered from back to front. To decide whether an object is transparent or not, the default implementation checks the [BlendingAttribute#blended](https://github.com/libgdx/libgdx/wiki/Material-and-environment#blendingattribute) value.

Customizing sorting can help increase performance. For example, sorting based on shader, mesh or used textures might help decreasing shader, mesh or texture switches. These kind of optimizations are very application specific. You can customize sorting by specifying your own [`RenderableSorter`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/utils/RenderableSorter.html) ([code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g3d/utils/RenderableSorter.java)) implementation while constructing the `ModelBatch`. This interface contains only one method:
```java
public void sort (Camera camera, Array<Renderable> renderables);
```
This method provides all information the `ModelBatch` has just before the actually rendering. It is also a very open API, you are allowed to modify the array as needed. This makes it possible to perform any last-minute actions (that might not be even related to sorting, like frustum culling) in this interface. The order of the `renderables` after this method completes, will be the order in which the render calls will be actually executed.

# The RenderContext
When not specified as argument, `ModelBatch` will create and manage a [`RenderContext`](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g3d/utils/RenderContext.html) for you. A RenderContext is used to minimize the overhead of OpenGL ES calls. For example, when a `Shader` requires backface culling and a previous shader enabled backface culling, then the redundant call isn't made.

By default, ModelBatch will reset this RenderContext on both the `begin()` and `end()` method. However, you can specify your own `RenderContext` (which doesn't have to be a custom implementation of it). When you specify your own RenderContext then you're responsible for calling the `context.begin()` and `context.end()` methods. This allows you use the same context for multiple ModelBatch instances.