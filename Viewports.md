When dealing with different screens it is often necessary to decide for a certain strategy how those different screen sizes and aspect ratios should be handled. Camera and Stage support different viewport strategies, for example when doing picking via Camera.project(Vector3 vec, viewportX, viewportY, viewportWidth, viewportHeight).

LibGDX provides a more convenient way of dealing with this problem, namely via Viewports.

### StretchedViewport
This implementation supports working with a virtual screen size. That means one can assume that a screen is always of the size VirtualWidth x VirtualHeight. This virtual viewport will then always be stretched to fit the screen. There are no black bars, but the aspect ratio may not be the same after the scaling took place.

### FixedViewport
A FixedViewport also supports a virtual screen size. The different to StretchedViewport is that it will always maintain the aspect ratio of the virtual screen size (virtual viewport), while scaling it as much as possible to fit the screen. One disadvantage with this strategy is that there may appear black bars.

### ScreenViewport
This viewport does not have a constant virtual screen size. The viewport will always match the window size which means that no scaling happens and no black bars appear. As a disadvantage this means that the gameplay might change, because a player with a bigger screen might see more of the game, than a player with a smaller screen size.

### DoubleRatioViewport (Coming Soon...)
This viewport gets a minimum and a maximum aspect ratio and interpolates the viewport between those two values. It may add black bars in case the devices screen aspect ratio is not in the range.

### MinMaxViewport (Coming Soon...)
This keeps the viewport in between a minimum and a maximum virtual viewport. It will select the viewport that fits the current screen the most and then scales it up to the screen size. It may add black bars in case the devices screen aspect ratio is not in the range.

### Additional viewports
Different strategies may be implemented by extending Viewport and implementing calculateViewport().

### How to use
A viewport always manages a Camera's viewportWidth and viewportHeight. Thus a camera needs to be supplied to the constructors. Whenever a resize event occurs, the viewport needs to be informed about it and updated via Viewport.update(width, height). This will automatically recalculate the viewport parameters and update the camera. Furthermore it will change the OpenGL Viewport via glViewport, which may add black bars if necessary, making it impossible to render in the area of the black bars.

A Viewport may be disabled by calling Viewport.disable() will will reset the OpenGL viewport to the maximum size, making it possible to render nice borders in the area of the black bars.

In case picking needs to be done, Viewport offers convenient project/unproject/getPickRay method, which uses the current viewport to do the correct picking.

In case of Stage being used, the Stage's Camera needs to be supplied to the viewport and Viewport.updateStage(Stage) needs to be called when a resize event happens.