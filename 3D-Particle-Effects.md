Becomes of issues with perspective and depth, the 2D particle effects are not suitable for 3D applications.  Additionally, the 3d particle effects allow for particles to take full advantage of movement through 3d space, allowing a wide variety of dynamic graphical effects.

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

### Basic Usage Example
```java

PointSpriteParticleBatch pointSpriteBatch;
BillboardParticleBatch billBatch;
AssetManager assets;
ParticleSystem particleSystem;

public void initPFX() {
	assets = new AssetManager();
	//assets.load("particle/particle_prealpha_multiplied.png", Texture.class);

	//emitters = new Array<ParticleController>();
	boolean useGPU = false;
	billBatch = new BillboardParticleBatch(ParticleShader.AlignMode.ViewPoint, useGPU, 100);
	billBatch.setCamera(cam);
	pointSpriteBatch = new PointSpriteParticleBatch();
	pointSpriteBatch.setCamera(cam);
	/*Array<ParticleBatch<?>> partBatches = new Array<>();
	partBatches.add(billBatch);*/
	particleSystem = ParticleSystem.get();
	particleSystem.add(billBatch);
	particleSystem.add(pointSpriteBatch);

	ParticleEffectLoader.ParticleEffectLoadParameter loadParam = new ParticleEffectLoader.ParticleEffectLoadParameter(particleSystem.getBatches());
	ParticleEffectLoader loader = new ParticleEffectLoader(new InternalFileHandleResolver());
	assets.setLoader(ParticleEffect.class, loader);
	assets.load("particle/dust.pfx", ParticleEffect.class, loadParam);
	assets.load("particle/explode.pfx", ParticleEffect.class, loadParam);
	assets.finishLoading();
}

public ParticleEffect createDustPFXCopy() {
	ParticleEffect original = assets.get("particle/dust.pfx");
	ParticleEffect dust = original.copy();
	dust.init();
	dust.start();
	particleSystem.add(dust);
	return dust;
}

public ParticleEffect createExplodePFXCopy() {
	ParticleEffect original = assets.get("particle/explode.pfx");
	ParticleEffect explode = original.copy();
	particleSystem.add(explode);
	explode.init();
	return explode;
}

```