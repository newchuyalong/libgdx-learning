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

# Using 3D Particle Effects
The easiest way to use 3D particle effects is by taken advantage of the ParticleSystem class, which acts somewhat like a SpriteBatch or ModelBatch class, abstracting away various details and managing them for you. First we will create the batch of the type(s) we wish to use, then create the ParticleSystem.  In this case, we are going to use PointSprites

### Step 1: Create Batches and ParticleSystem
```java
// ParticleSystem is a singleton class, we get the instance instead of creating a new object:
ParticleSystem particleSystem = ParticleSystem.get();
PointSpriteParticleBatch pointSpriteBatch = new PointSpriteParticleBatch();
pointSpriteBatch.setCamera(cam);
particleSystem = ParticleSystem.get();
particleSystem.add(pointSpriteBatch);
```

### Step 2: Load Effects Using AssetManager
Now we need to load our particle effects that we have created using the Flame GUI editor.
First, create a ParticleEffectLoadParameter to pass to the asset manager when loading.
Then the assets may be loaded.
```java
AssetManager assets = new AssetManager();
ParticleEffectLoader.ParticleEffectLoadParameter loadParam = new ParticleEffectLoader.ParticleEffectLoadParameter(particleSystem.getBatches());
		ParticleEffectLoader loader = new ParticleEffectLoader(new InternalFileHandleResolver());
assets.setLoader(ParticleEffect.class, loader);
assets.load("particle/explode.pfx", ParticleEffect.class, loadParam);
assets.finishLoading()
```

### Step 3: Add Loaded ParticleEffects to the ParticleSystem
```java
ParticleEffect effect = assets.get("particle/dust.pfx");
effect.init();
effect.start();  // optional: particle will begin playing immediately
particleSystem.add(effect);
```

### Step 4: Rendering our 3D Particles Using the ParticleSystem
A ParticleSystem must update and draw its own components, then be passed to a ModelBatch instance to be rendered to the scene.
```java
private void renderParticleEffects() {
	particleSystem.begin();
	particleSystem.draw();
	particleSystem.end();
	particleSystem.update();
	modelBatch.render(particleSystem);
}
```

