When working with libGDX (or any other OpenGL based system), you will have to deal with various [coordinate systems](https://en.wikipedia.org/wiki/Coordinate_system). This is because OpenGL abstracts away device dependent units, making it more convenient to target multiple devices and to focus on game logic. Sometimes you may need to convert between coordinate systems for which libGDX offers various methods.

**It is crucial to understand in which coordinate system you are working. Otherwise it can be easy get confused and to make assumptions which aren't correct.**

On this page the various coordinate systems are listed. It is highly recommended to first get familiar with the [Cartesian coordinate system](https://en.wikipedia.org/wiki/Cartesian_coordinate_system), which is the most widely used coordinate system.

## Touch coordinates
 * Units: pixels
 * System: y-down
 * Type: integer, can't be fractional
 * Range: [0,0] *inclusive* (upper left corner) to (`Gdx.graphics.getWidth()`, `Gdx.graphics.getHeight()`) *exclusive* (lower right corner)
 * Usage: touch/mouse coordinates
 * Dependence: device specific

Starts at the upper left *pixel* of the (application portion) of the physical screen and has the size of the (application portion) of the physical screens width and height in pixels.

Each coordinate is an index in the 2D array of this grid, representing a physical pixel on the screen. Therefor these coordinates are always represented as integers, they can't be fractional.

This coordinate system is based on the classic representation of the display, which is usually also closest to the device/OS specific implementation. If you're familiar with canvas graphics or basic image editors, then you are probably already familiar with these coordinates. You might even lean towards using these as your default/favorite, which you shouldn't.

Whenever working with [mouse or touch](https://github.com/libgdx/libgdx/wiki/Mouse%2C-touch-%26-keyboard#mouse--touch) coordinates, you'll be using this coordinate system. You typically want to convert these coordinates as soon as possible to a more convenient coordinate system. E.g. the `camera.unproject` or `viewport.unproject` method let's you convert those them to world coordinates (see below).

## Screen or image coordinates
 * Units: pixels
 * System: y-up
 * Type: integer, can't be fractional
 * Range: [0,0] *inclusive* (lower left corner) to (`Gdx.graphics.getWidth()`, `Gdx.graphics.getHeight()`) *exclusive* (upper right corner)
 * Usage: viewport, scissors and pixmap
 * Dependence: device/resource/asset specific

This is OpenGL's counterpart to touch coordinates; that is: it is used to specify (index) a pixel of the (portion of the) physical screen. It is also used as indexer for an image in memory. Likewise, these are integers, they can't be fractional.

The only difference between touch and screen coordinates is that touch coordinates are y-down, while screen coordinates are y-up. Converting between them is therefor quite easy:

```java
y = Gdx.graphics.getHeight() - 1 - y;
```

You typically use this coordinates to specify which portion of the screen to render onto. For example when calling [`glViewport`](https://www.khronos.org/opengles/sdk/1.1/docs/man/glViewport.xml), [`glScissor`](https://www.khronos.org/opengles/sdk/1.1/docs/man/glScissor.xml) or manipulating a pixmap (see next). In the majority of use-cases you don't need this coordinate system alot, if any, and it should be isolated from your game logic and coordinate system. The `camera.project` and `viewport.project` methods can be used to convert world units to screen coordinates.

### Pixmap and texture coordinates

Pixmap coordinates are an exception. Pixmaps are commonly used to upload texture data. For example when loading a PNG image file to a texture, it is first decoded (uncompressed) to a Pixmap, which is the raw pixel data of the image, then it is copied to the GPU for use as texture. The texture can then be used to render to the screen. It is also possible to modify or create a pixmap by code, e.g. before uploading as texture data.

The "problem" with this is that OpenGL expects the texture data to be in image coordinates, which is y-up. However, most image formats store the image data comparable to touch coordinates, which is y-down. LibGDX does not translate the image data between the two (which would involve copying the image line by line), instead it simply copied the data as is. This practically causes a Texture loaded from Pixmap to be up-side-down.

To compensate for this up-side-down texture, `SpriteBatch` flips the texture coordinates on the y axis when rendering. Likewise, fbx-conv has the option to flip texture coordinates on the y axis as well. However, when you use a texture which isn't loaded from a pixmap, for example a Framebuffer, then this might cause that texture to appear up-side-down.

## Normalized render coordinates
 * Units: one
 * System: y-up
 * Type: floating point
 * Range: [-1,-1] *inclusive* (lower left corner) to [+1,+1] *inclusive* (upper right corner)
 * Usage: shaders
 * Dependence: none

The above coordinate systems have one big issue in common: they are device specific. To solve that, OpenGL allows you to use a device independent coordinate system which is automatically mapped to screen coordinates when rendering. This coordinate system is normalized in the range [-1,-1] and [+1,+1] with (0,0) exactly in the center of the screen (render target).

The *vertex shader* outputs (`gl_Position`) its coordinates in this coordinate system. But other than that, you should never have to use this coordinate system in a practical use-case. It is sometimes used in tutorials and such, though, to show the basics.

It might be worth to note that the normalization does not respect the aspect ratio. That is: the scale in the X direction does not have to match the scale in the Y direction. They are both within the range of `-1` to `+1`, regardless aspect ratio. It is up to the application to decide how to deal with various aspect ratios.

The coordinates are floating point and no longer indexers. The device (GPU) will map these coordinates to the actual screen pixels using [rasterisation](https://en.wikipedia.org/wiki/Rasterisation). A good article (although targeting DirectX it also applies to OpenGL) for more information on that can be found [here](https://msdn.microsoft.com/en-us/library/windows/desktop/cc627092(v=vs.85).aspx).

## Normalized texture coordinates
 * Units: one
 * System: y-up
 * Type: floating point
 * Range: [0,0] *inclusive* (lower left corner) to [+1,+1] *inclusive* (upper right corner)
 * Usage: shaders, uv coordinates
 * Dependence: none

Likewise to the normalized render coordinates, OpenGL typically also uses normalized texture coordinates. The only difference is that these ranges from [0,0] to [1,1]. Depending on the specified wrap function, values outside that range will be mapped within that range.

These coordinates are also called **UV coordinates**. In many use cases you don't have to deal with them. Typically these values are stored in the mesh.

## World coordinates
 * Units: application specific, e.g. [SI Units](https://en.wikipedia.org/wiki/International_System_of_Units)
 * System: y-up
 * Type: floating point
 * Range: application specific
 * Usage: game logic
 * Dependence: game/application

Typically, your game logic should use a coordinate system which best fits the game logic. It should not depend on device or asset size. For example, a commonly used unit is meters.

The world coordinates are converted, in the vertex shader, to normalized render coordinates. The Camera or Viewport is used to define the strategy on how to do that. For example, to maintain aspect ratio, black bars can be added. But in the end, one specific world coordinate will map to the lower left corner of the screen and another world coordinate will map to the upper right corner of the screen.