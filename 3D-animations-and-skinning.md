A Model (and ModelInstance) can contain one or more animations. An animation is a sequence (keyframes) of transformations which are applied to one or more nodes of the model. Each animation is identified by it's name (id), which must be unique within the model. E.g. you might have character with an animation called "walk" and "attack". This page describes how to load and use animations within your application.

Skinning can be used to deform the model (mesh) based on the transformation of one or more nodes. This is commonly used conjunction with animations. The page also describes how to use skinning within your application.

# Loading animations
When using fbx-conv to convert your model from FBX to G3DB/G3DJ, animations are automatically converted along with it. Just like FBX, G3DB/G3DJ files can contain multiple animations in a single file along with the other model data. Animations applied to nodes which are not present in the source FBX file, will not be converted. So make sure to select all the nodes and animations when exporting to FBX.

You can check the converted animations by converting to the G3DJ file format and opening the resulting file in your favorite text editor. The animations are located at the bottom of the file. Be aware that animations (keyframes) can bloat the file, so it's advised to use the smaller G3DB file format on production.

LibGDX does not support custom interpolation between keyframes. If you use an interpolation other than linear, fbx-conv will generate additional keyframes to compensate this. This compensation is done according to the target FPS. While the target FPS is customizable in FBX, not all modeling applications allow you to change this value (the default is 30 FPS). To avoid bloating the file with additional keyframes, make sure to use linear interpolation whenever possible.

When you load the G3DB/G3DJ file in your application using G3dModelLoader or AssetManager, the animations are also loaded. When you create a ModelInstance from the Model, the Animations are also copied to the ModelInstance. If you specify specific nodes while creating the ModelInstance, only the animations that are applied to those nodes (and child nodes) are copied to the new ModelInstance.

# Using animations
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

The `setAnimation(String id)` method will play the animation once at normal speed and then stop (freezing the animation at the last frame of the animation). 