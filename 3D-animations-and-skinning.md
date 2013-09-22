A Model (and ModelInstance) can contain one or more animations. An animation is a sequence (keyframes) of transformations which are applied to one or more nodes of the model. Each animation is identified by it's name (id), which must be unique within the model. E.g. you might have character with an animation called "walk" and "attack". This page describes how to load and use animations within your application.

Skinning can be used to deform the model (mesh) based on the transformation of one or more nodes. This is commonly used conjunction with animations. The page also describes how to use skinning within your application.

# Loading animations
When using fbx-conv to convert your model from FBX to G3DB/G3DJ, animations are automatically converted along with it. Just like FBX, G3DB/G3DJ files can contain multiple animations in a single file along with the other model data. Animations applied to nodes which are not present in the source FBX file, will not be converted. So make sure to select all the nodes and animations when exporting to FBX.

You can check the converted animations by converting to the G3DJ file format and opening the resulting file in your favorite text editor. The animations are located at the bottom of the file. Be aware that animations (keyframes) can bloat the file, so it's advised to use the smaller G3DB file format on production.

LibGDX does not support custom interpolation between keyframes. If you use an interpolation other than linear, fbx-conv will generate additional keyframes to compensate this. This compensation is done according to the target FPS. While the target FPS is customizable in FBX, not all modeling applications allow you to change this value (the default is 30 FPS). To avoid bloating the file with additional keyframes, make sure to use linear interpolation whenever possible.

When you load the G3DB/G3DJ file in your application using G3dModelLoader or AssetManager, the animations are also loaded. When you create a ModelInstance from the Model, the Animations are also copied to the ModelInstance. If you specify specific nodes while creating the ModelInstance, only the animations that are applied to those nodes (and child nodes) are copied to the new ModelInstance.

# Using animations
## Applying a single animation
To apply an animation to a ModelInstance, you can create an AnimationController. An AnimationController is dedicated to a single ModelInstance and the ModelInstance must outlive the AnimationController. The AnimationController can be used to apply a single animation to the ModelInstance at a time, although it support blending (transition) between multiple animations. If you want to apply multiple animations to the same ModelInstance, you can use multiple AnimationControllers, as long as they don't interfere with each other (don't affect the same nodes).

When an animation is set (described below), the AnimationController needs to be updated every frame. This can be done using the `AnimationController.update(float delta)` method. The `delta` argument is used to inform the AnimationController how much time is past since the last call to update. Most commonly `Gdx.graphics.getDeltaTime()` is used for that parameter.

To set the current animation use the `setAnimation(String id)` method. This will cause the animation to be played immediately (removing the current animation, if any). The `id` argument must be the name (case sensitive) of an existing animation within the ModelInstance.
```java
ModelInstance instance;
AnimationController controller;
public void create() {
    ...
    instance = new ModelInstance(model);
    controller = new AnimationController(instance);
    controller.setAnimation("walk");
}
public void render() {
    ...
    controller.update(Gdx.graphics.getDeltaTime());
    ...
}
```

The `setAnimation(String id)` method will play the animation once at normal speed and then stop (freezing the animation at the last frame of the animation). To remove the animation (so the nodes are at their original transformation), call `setAnimation(null)`.

### Looping
If you want to execute an animation more than once, you can use the `setAnimation(String id, int loopCount)` method. The `loopCount` argument can be used to specify how many times the animation should be played (which must not be zero). To continuous loop the animation use the value `-1`.

## AnimationDesc
Every animation method (like `setAnimation(...)` returns an `AnimationDesc` instance, which is valid while the animation is being played (or queued to be played, see below). The AnimationDesc contains all information about the animation, like the current animation time and the number of loops remaining for that animation. You can alter those values to control the animation during execution. When the animation is ended, you must remove all references to the AnimationDesc, as it might be reused (pooled) for a next animation.

## AnimationListener
To get notified when an animation is looped or ended you can implement the `AnimationListener` interface. It will be notified when an animation is ended or looped. It receives the AnimationDesc of the animation that ended or looped. You can supply your AnimationListener with every animation method, e.g. `setAnimation(String id, int loopCount, AnimationListener listener)`.

## Blending animations
When switching animation using the `setAnimation`, the previous animation is stopped abruptly. It is possible to blend to previous animation into the new animation, giving a smooth transition between animations. You can use the `animate(...)` for that. The animate method takes the same parameters as the setAnimation method along with an additional `transitionTime` argument. During this time the new animation will be linear interpolated on top of the old animation, resulting in a smooth transition. If there is no previous animation, then the new animation will start immediately.

## Queuing animations
To start an animation when the current animation is completed, you can use the `queue(...)` method. If the current animation is continuous looping, the queued animation will be executed when the current loop completed. Otherwise the queued animation will be executed when the current animation is ended (including all loops remaining). If there is no current animation, the queued animation will be executed immediately. Queued animations will be blended if a transition time greater than zero is specified.

## Action animation
An action animation is a (commonly short) animation that is executed on top of the current the animation. In practice, it is the same calling `animate(...)` with the action animation and calling `queue(...)` with the previous animation.
