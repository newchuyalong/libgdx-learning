When dealing with different screens it is often necessary to decide for a certain strategy how those different screen sizes and aspect ratios should be handled. Camera and Stage support different viewport strategies, for example when doing picking via `Camera.project(vec, viewportX, viewportY, viewportWidth, viewportHeight)`.

LibGDX provides a more convenient way of dealing with this problem, namely via `Viewport`s.

### StretchViewport
The `StretchViewport` supports working with a virtual screen size. That means one can assume that a screen is always of the size `virtualWidth x virtualHeight`. This virtual viewport will then always be stretched to fit the screen. There are no black bars, but the aspect ratio may not be the same after the scaling took place.

![StretchViewport](http://i.imgur.com/oheUy0y.png)

### FitViewport
A `FitViewport` also supports a virtual screen size. The difference to StretchViewport is that it will always maintain the aspect ratio of the virtual screen size (virtual viewport), while scaling it as much as possible to fit the screen. One disadvantage with this strategy is that there may appear black bars.

![FitViewport](http://i.imgur.com/Kv2wB94.png)

### FillViewport
`FillViewport` also keeps the aspect ratio of the virtual screen size, but in contrast to FitViewport, it will always fill the whole screen which might result in parts of the viewport being cut off.

### ScreenViewport
This viewport does not have a constant virtual screen size. The `ScreenViewport` will always match the window size which means that no scaling happens and no black bars appear. As a disadvantage this means that the gameplay might change, because a player with a bigger screen might see more of the game, than a player with a smaller screen size.

![Screen Viewport](http://i.imgur.com/qtOytdq.png)

### ExtendViewport
The `ExtendViewport` keeps the world aspect ratio without black bars by extending the world in one direction. The world is first scaled to fit within the viewport, then the shorter dimension is lengthened to fill the viewport.

### DoubleRatioViewport (Coming Soon...)
This keeps the viewport in between a minimum and a maximum aspect ratio. It will select the aspect ratio that fits the current screen the most and then scales it up to the screen size. It may add black bars in case the devices screen aspect ratio is not in the range.

### MinMaxViewport (Coming Soon...)
This viewport gets a minimum and a maximum viewport and interpolates the viewport between those two values so that it fits the screen size the most. It may add black bars in case the devices screen aspect ratio is not in the range.

### CustomViewport
Different strategies may be implemented by doing `CustomViewport extends Viewport` and implementing `calculateViewport(width, height)`. Another approach is use the generic `ScalingViewport` and supplying another Scaling which is not yet covered by any other Viewport. One example could be to supply `Scaling.none` to it, which will result in a completely "StaticViewport", which always keeps the same size. It might look like this:

![StaticViewport](http://i.imgur.com/8F697TX.png)

### Usage
A viewport always manages a Camera's viewportWidth and viewportHeight. Thus a camera needs to be supplied to the constructors.

    private Viewport viewport;
    private Camera camera;

    public void create() {
        camera = new PerspectiveCamera();
        viewport = new FixedViewport(camera, 800, 480);
    }

Whenever a resize event occurs, the viewport needs to be informed about it and updated. This will automatically recalculate the viewport parameters and update the camera:

    public void resize(int width, int height) {
        viewport.update(width, height);
    }

Furthermore it will change the OpenGL Viewport via glViewport, which may add black bars if necessary, making it impossible to render in the area of the black bars. In case black bars appear with a certain viewport strategy, the OpenGL viewport may be reset to it's standard size and the viewport can be queried for the size of the bars via `Viewport.getLeftGutterWidth()` etc. For an example of how to do so, see [this test](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/ViewportTest2.java). This might look like the following (probably with a more appropriate border picture...)

![Border](http://i.imgur.com/OVamVTh.png?1)

In case picking needs to be done, Viewport offers convenient `project/unproject/getPickRay` methods, which uses the current viewport to do the correct picking.

In case of Stage being used, the Stage's Camera needs to be supplied to the viewport and `Viewport.updateStage(Stage)` needs to be called when a resize event happens.

    private Stage stage;

    public void create() {
        stage = new Stage(new StretchViewport(width, height));
    }

    public void resize(int width, int height) {
        // use true here in case of a UI-only stage to center the camera
        stage.getViewport().update(width, height, false);
    }

To see the viewports in action, have a look at the tests here: [ViewportTest1](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/ViewportTest1.java), [ViewportTest2](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/ViewportTest2.java) and [ViewportTest3](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/ViewportTest3.java).