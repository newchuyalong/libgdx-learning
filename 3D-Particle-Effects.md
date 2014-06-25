Becomes of issues with perspective and depth, the 2D particle effects are not suitable for 3D applications.  Additionally, the 3d particle effects allow for particles to take full advantage of movement through 3d space, allowing a wide variety of dynamic graphical effects.

![3D particles in action](http://www.pixelscientists.com/images/flamedemo.gif)

### Flame - 3D Particle Editor
Much like their 2D cousins, 3D particle effects can be edited with a GUI editor included in libgdx.
The editor is called flame, and is located in the non-core gdx-tools.jar.  If you have a clone of the libgdx repo, you can run the editor by using the following command from the **dist** directory:

Linux/Mac:
```
java -cp gdx.jar:gdx-natives.jar:gdx-backend-lwjgl.jar:gdx-backend-lwjgl-natives.jar:extensions/gdx-tools/gdx-tools.jar com.badlogic.gdx.tools.flame.FlameMain
```

Windows:
```
java -cp gdx.jar;gdx-natives.jar;gdx-backend-lwjgl.jar;gdx-backend-lwjgl-natives.jar;extensions/gdx-tools/gdx-tools.jar com.badlogic.gdx.tools.flame.FlameMain
```

There is also an externally maintained (probably out of date) stand-alone jar you can download here: 

[http://www.pixelscientists.com/downloads/flame.jar](http://www.pixelscientists.com/downloads/flame.jar)

Run this jar with: `java -jar flame.jar`

### Particle Effect Types
There are 3 different kinds of 3d particle effects:
* Billboards
* PointSprites
* ModelInstance
* TODO: ParticleController??

**Billboards** are sprites that always face the camera (the Decal class in libgdx is essentially a billboard)

**PointSprites** are simpler (more efficient, better performance) that billboards.  They are colored points with no textures.  The dust and explosion effects in the following video are done with PointSprites:

[3D Tank Battle video](https://www.youtube.com/watch?v=lt9kYDb9p78)

**ModelInstances** are familiar to you if you have done any 3D work in libgdx.  They are instances of 3D models.  Not surprisingly, this is the most taxing type of particle effect in terms of performance.

