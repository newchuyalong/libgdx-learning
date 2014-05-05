The libgdx Particle Editor is a powerful tool for making particle effects. See the video and documentation below. The Java API works (the editor is built using it) but could use some clean up and definitely some documentation. There is a [runnable example](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/ParticleEmitterTest.java), though unfortunately it isn't the simplest or typical usage. Improvements to particles is planned, but will be some time until we can get to it.

## Running the Particle Editor ##

To run the editor, you can check out libgdx and run it [[from source | Running Demos & Tests]]. The editor is in the gdx-tools project. Alternatively, you can download the [nightly build](http://libgdx.badlogicgames.com/nightlies/) and run the particle editor from the gdx-tools JAR:

Windows:

```
java -cp gdx.jar;gdx-natives.jar;gdx-backend-lwjgl.jar;gdx-backend-lwjgl-natives.jar;extensions\gdx-tools\gdx-tools.jar com.badlogic.gdx.tools.particleeditor.ParticleEditor
```

Linux or Mac:

```
java -cp gdx.jar:gdx-natives.jar:gdx-backend-lwjgl.jar:gdx-backend-lwjgl-natives.jar:extensions/gdx-tools/gdx-tools.jar com.badlogic.gdx.tools.particleeditor.ParticleEditor
```

Click this image for the JWS, though this is not as up to date as running from source or from gdx-tools.jar:

[[images/particle-editor.png]]

[download](http://wiki.libgdx.googlecode.com/git/jws/particle-editor.jnlp)

## Using the Particle Editor ##

Video:

<a href="http://www.youtube.com/watch?feature=player_embedded&v=w8xkf3O4nho
" target="_blank"><img src="http://img.youtube.com/vi/w8xkf3O4nho/0.jpg" 
alt="Particle Editor Usage" width="480" height="360" border="10" /></a>

Also see this [tutorial on JGO](http://www.java-gaming.org/topics/particle-effects-in-libgdx/29484/view.html).

Briefly, a particle effect consists of some images that are moved around. The images usually use additive blending and some pretty stunning results can be produced with only a few images. Particle effects are good for fire, explosions, smoke, etc. Each particle has many properties that control how it behaves: life, velocity, rotation, scale, etc. The particle editor allows you to manipulate these properties and observe the result in real time. You can also create effects programmatically, but it is much more difficult and time consuming to create great effects.

The first step to creating an effect is to choose an image. The default image is just a simple round gradient. Experiment with different images to create a wide variety of effects. Images will often combine for some surprising and sometimes very cool looking results.

When you are configuring properties, you are actually configuring the particle emitter that will create and manage the particles. In code, the emitter is represented by the ParticleEmitter class. A particle effect is made up of one or more emitters, which is managed in the lower right of the particle editor. In code, the effect is represented by the ParticleEffect class, which has a list of ParticleEmitters. Most of emitter properties are self explanatory, but I will run through them quickly.

**Delay:** When an effect starts, this emitter will do nothing for this many milliseconds. This can be used to synchronize multiple emitters.

Note the delay property has an “Active” button. Some properties can be turned off, which can minimize some of the work that needs to be done at run-time.

**Duration:** How long the emitter will emit particles. Note this is not the same as how long particles will live.

**Count:** Controls the minimum number of particles that must always exist, and the maximum number of particles that can possibly exist. The minimum is nice for making sure particles are always visible, and the maximum lets the emitter know how much memory to allocate.

**Emission:** How many particles will be emitted per second.

Emission is the first property with a little chart, but all properties with a chart work the same way. Click on the chart to add nodes, click and drag to move nodes, and double click to delete nodes. The chart allows you to vary the value of the property over time. Note that the chart says “Duration” in the center. This means the x-axis represents the duration of the emitter (which of course is set with the duration property). Other charts may say “Life”, which means the x-axis represents the life of a single particle. The top of the y-axis represents the value specified for “High” and the bottom of the y-axis represents the value specified for “Low”. The chart can be expanded with the “+” button for a larger view.

The “High” and “Low” values each have a button marked “>”. When clicked, this will expand to show a second text box. A random number (float) is chosen between the text box on the left and the one on the right. The number is chosen when the effect starts for a “Duration” chart, and when a particle is spawned for a “Life” chart.

Finally, the last piece of functionality for charts is the “Relative” check-box. When unchecked, the value at any one point in time for the property will be what the chart shows. When checked, the value shown on the chart is added to the initial value of the property. Why? Imagine you have rotation set to start at 0 and go to 360 degrees over the life of a particle. This is nice, but all the particles start at the same zero rotation, so you change the “Low” value to start between 0 and 360. Now your particles will start between 0 and 360, and rotate to exactly 360 degrees. If a particle spawns at 330 degrees, it will only rotate 30 degrees. Now, if you check “Relative”, a particle that spawns at 330 degrees will rotate to 330 + 360 degrees, which is probably what you want in this case.

**Life:** How long a single particle will live.

**Life Offset:** How much life is used up when a particle spawns. The particle is still moved/rotated/etc for the portion of its life that is used up. This allows particles to spawn, eg, halfway through their life.

**X Offset and Y Offset:** The amount in pixels to offset where particles spawn.

**Spawn:** The shape used to spawn particles: point, line, square, or ellipse. Ellipse has additional settings.

**Spawn Width and Spawn Height:** Controls the size of the spawn shape.

**Size:** The size of the particle.

**Velocity:** The speed of the particle.

**Angle:** The direction the particle travels. Not very useful if velocity is not active.

**Rotation:** The rotation of the particle.

**Wind and Gravity:** The x-axis or y-axis force to apply to particles, in pixels per second.

**Tint:** The particle color. Click the little triangle and then use the sliders to change the color. Click in the bar above the triangle to add more triangles. This allows you to make particles change to any number of colors over their lifetime. Click and drag to move a triangle (if it isn’t at the start or end), double click to delete.

**Transparency:** Controls the alpha of the particle.

**Options, Additive:** For additive blending.
Options, Attached: Means existing particles will move when the emitter moves.
Options, Continuous: Means the emitter restarts as soon as its duration expires. Note that this means an effect will never end, so other emitters in the effect that are not continuous will never restart.
Options, Aligned: The angle of a particle is added to the rotation. This allows you to align the particle image to the direction of travel.

In the upper left of the particle editor, “Count” shows how many particles exist for the currently selected emitter. “Max” shows how many particles exist for all emitters over the past few seconds. Below that is a percentage that represents the duration percent of the currently selected emitter.

Effect settings saved with the particle editor are written to a text file, which can be loaded into a ParticleEffect instance in your game. The ParticleEffect can load images from a directory, or a SpriteSheet. Of course, a SpriteSheet is recommended and can easily be made with the SpriteSheetPacker.

Most effects can be simplified to use just a few images. My most complex effects that use 4 or more emitters typically only need 15 or so total particles alive at once. My crappy Droid can render 250 32×32 blended particles at 46 fos, 500 particles at 36 fps, and 1000 particles at 26 fps (see ParticleEmitterTest in gdx-tests). However, the performance varies greatly with the particle image size.

## loading Particle Effects in your game ##

see [2d-particle-effects](2d-particle-effects)
