# Interpolation #

Commonly known as _tweening_, [interpolation](http://en.wikipedia.org/wiki/Interpolation) is useful for generating values between two discrete end points using various curve functions. Often used with key-framed animation, interpolation allows an animator to specify a sparse collection of explicit frames for an animation and then generate a smooth transition between these frames computationally. The simplest form of interpolation is linear interpolation such as that available directly in the [Vector2](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/math/Vector2.html) [(code)](https://github.com/libgdx/libgdx/tree/master/gdx/src/com/badlogic/gdx/math/Vector2.java) and [Vector3](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/math/Vector3.html) [(code)](https://github.com/libgdx/libgdx/tree/master/gdx/src/com/badlogic/gdx/math/Vector3.java) classes. The [Interpolation](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/math/Interpolation.html) [(code)](https://github.com/libgdx/libgdx/tree/master/gdx/src/com/badlogic/gdx/math/Interpolation.java) class provides more interesting results by using non-linear curve functions to interpolate values.

## Types of Interpolation ##

These are the basic built-in types of interpolation:

  * Bounce
  * Circle
  * Elastic
  * Exponential
  * Fade
  * Power
  * Sine
  * Swing

Most types offer three varieties which bias towards one or the other or both ends of the curve creating an easing in or out of the animation.

See [InterpolationTest](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/InterpolationTest.java) for a visual display of each interpolation.