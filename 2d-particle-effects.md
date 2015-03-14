See [this](2D-Particle-Editor) article on how to use the ParticleEditor.

Documentation is currently lacking for libgdx particles, sorry.

There is a video tutorial though:

<a href="http://www.youtube.com/watch?feature=player_embedded&v=LCLa-rgR_MA
" target="_blank"><img src="http://img.youtube.com/vi/LCLa-rgR_MA/0.jpg" 
alt="Libgdx 2D Particle Effects" width="480" height="360" border="10" /></a>



  * [source](https://bitbucket.org/dermetfan/somelibgdxtests/src/207cfc0a6123b48200d5cf721df222cbe7faf1be/src/net/dermetfan/someLibgdxTests/screens/ParticleEffectsTutorial.java?at=default) of the video
  * [source](https://bitbucket.org/dermetfan/somelibgdxtests/src/4582a1bf94bded4f30df47b9195d1ae14728b847/src/net/dermetfan/someLibgdxTests/screens/ParticleEffectsTutorial.java?at=default) of the video using [pooling](https://www.youtube.com/watch?v=3OwIiELYa70)

Pooled effect example:
```java
ParticleEffectPool bombEffectPool;
Array<PooledEffect> effects = new Array();

...

//Set up the particle effect that will act as the pool's template
ParticleEffect bombEffect = new ParticleEffect();
bombEffect.load(Gdx.files.internal("particles/bomb.p"), atlas);

//If your particle effect includes additive or pre-multiplied particle emitters
//you can turn off blend function clean-up to save a lot of draw calls, but
//remember to switch the Batch back to GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA
//before drawing "regular" sprites or your Stage.
bombEffect.setEmittersCleanUpBlendFunction(false);

bombEffectPool = new ParticleEffectPool(bombEffect, 1, 2);

...

// Create effect:
PooledEffect effect = bombEffectPool.obtain();
effect.setPosition(x, y);
effects.add(effect);

...

// Update and draw effects:
for (int i = effects.size - 1; i >= 0; i--) {
	PooledEffect effect = effects.get(i);
	effect.draw(batch, delta);
	if (effect.isComplete()) {
		effect.free();
		effects.removeIndex(i);
	}
}

...

// Reset all effects:
for (int i = effects.size - 1; i >= 0; i--)
    effects.get(i).free();
effects.clear();
