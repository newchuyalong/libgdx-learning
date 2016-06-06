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


## Visual display of interpolations ##
bounce | bouncein | bounceout | circle
--- | --- | --- | ---
![bounce](https://cloud.githubusercontent.com/assets/1666014/15840432/9ab76fd2-2c52-11e6-929f-eff05b5d9360.png) | ![bouncein](https://cloud.githubusercontent.com/assets/1666014/15840433/9ad3b0ca-2c52-11e6-8c90-e0f5fcb88928.png) | ![bounceout](https://cloud.githubusercontent.com/assets/1666014/15840436/9ae02e68-2c52-11e6-8fb0-e82e23abe446.png) | ![circle](https://cloud.githubusercontent.com/assets/1666014/15840435/9ae022b0-2c52-11e6-9701-200e45c82749.png)
circlein | circleout | elastic | elasticin
![circlein](https://cloud.githubusercontent.com/assets/1666014/15840437/9ae164cc-2c52-11e6-9939-526f88ed6753.png) | ![circleout](https://cloud.githubusercontent.com/assets/1666014/15840438/9ae1fbc6-2c52-11e6-97c6-f5a37aa59673.png) | ![elastic](https://cloud.githubusercontent.com/assets/1666014/15840434/9adda9a4-2c52-11e6-9daa-3f344215fa41.png) | ![elasticin](https://cloud.githubusercontent.com/assets/1666014/15840439/9aee683e-2c52-11e6-9821-a8453835a027.png)
elasticout | exp5 | exp5in | exp5out
![elasticout](https://cloud.githubusercontent.com/assets/1666014/15840440/9afa4ae6-2c52-11e6-9bf4-08e7867328f9.png) | ![exp5](https://cloud.githubusercontent.com/assets/1666014/15840441/9afafc34-2c52-11e6-8dfa-c0212455f7c6.png) | ![exp5in](https://cloud.githubusercontent.com/assets/1666014/15840442/9afcc0e6-2c52-11e6-887c-00c8d6919321.png) | ![exp5out](https://cloud.githubusercontent.com/assets/1666014/15840443/9afd2d2e-2c52-11e6-9722-6c3713c65f91.png)
exp10 | exp10in | exp10out | fade
![exp10](https://cloud.githubusercontent.com/assets/1666014/15840444/9afdbf96-2c52-11e6-8212-9f2c0dfc000f.png) | ![exp10in](https://cloud.githubusercontent.com/assets/1666014/15840445/9b0dd408-2c52-11e6-846b-7b236cfc463e.png) | ![exp10out](https://cloud.githubusercontent.com/assets/1666014/15840446/9b14fa9e-2c52-11e6-939b-a57cac59ed6c.png) | ![fade](https://cloud.githubusercontent.com/assets/1666014/15840447/9b18e212-2c52-11e6-8da7-cdb6a0ec8775.png)
linear | pow2 | pow2in | pow2out
![linear](https://cloud.githubusercontent.com/assets/1666014/15840448/9b196f98-2c52-11e6-9a8f-40e674e6c47d.png) | ![pow2](https://cloud.githubusercontent.com/assets/1666014/15840450/9b1b0bbe-2c52-11e6-91ed-55dad91d50bb.png) | ![pow2in](https://cloud.githubusercontent.com/assets/1666014/15840449/9b1b08a8-2c52-11e6-93df-24b776427820.png) | ![pow2out](https://cloud.githubusercontent.com/assets/1666014/15840451/9b27ae5a-2c52-11e6-91b2-5943b413a77f.png)
pow3 |  pow3in| pow3out | pow4
![pow3](https://cloud.githubusercontent.com/assets/1666014/15840452/9b2c6b34-2c52-11e6-8e66-fe467a605220.png) | ![pow3in](https://cloud.githubusercontent.com/assets/1666014/15840456/9b3a16ee-2c52-11e6-9cc8-590ec43c1cc9.png) | ![pow3out](https://cloud.githubusercontent.com/assets/1666014/15840455/9b384c92-2c52-11e6-8eaa-8a9f28d945e6.png) | ![pow4](https://cloud.githubusercontent.com/assets/1666014/15840453/9b37c678-2c52-11e6-91bc-b2a566cb4f21.png)
pow4in | pow4out | pow5 | pow5in
![pow4in](https://cloud.githubusercontent.com/assets/1666014/15840454/9b384cba-2c52-11e6-92f5-6b39922f0d71.png) | ![pow4out](https://cloud.githubusercontent.com/assets/1666014/15840457/9b440e38-2c52-11e6-83f7-32fe9b6821f7.png) | ![pow5](https://cloud.githubusercontent.com/assets/1666014/15840458/9b4abc38-2c52-11e6-8131-931d41a6ef5c.png) | ![pow5in](https://cloud.githubusercontent.com/assets/1666014/15840459/9b5031cc-2c52-11e6-804b-bbb9a76b7043.png)
pow5out | sine | sinein | sineout
![pow5out](https://cloud.githubusercontent.com/assets/1666014/15840461/9b557e66-2c52-11e6-9274-4dd890d28849.png) | ![sine](https://cloud.githubusercontent.com/assets/1666014/15840462/9b561510-2c52-11e6-9bb7-1b8c0500a057.png) | ![sinein](https://cloud.githubusercontent.com/assets/1666014/15840460/9b54b7ce-2c52-11e6-8d91-f5c0acf0b83c.png) | ![sineout](https://cloud.githubusercontent.com/assets/1666014/15840463/9b65c64a-2c52-11e6-9df0-4ebe7436a7a7.png)
swing | swingin | swingout | -
![swing](https://cloud.githubusercontent.com/assets/1666014/15840464/9b6606aa-2c52-11e6-9dcc-eb05460f9b6f.png) | ![swingin](https://cloud.githubusercontent.com/assets/1666014/15840465/9b6ac5b4-2c52-11e6-965f-e3eec2bf1eab.png) | ![swingout](https://cloud.githubusercontent.com/assets/1666014/15840466/9b7159b0-2c52-11e6-922e-d6de2bd8f11a.png) | -