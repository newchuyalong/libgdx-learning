LibGdx makes use of bitmap files (pngs) to render fonts.  Each glyph in the font has a corresponding TextureRegion.

[BitmapFont class](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g2d/BitmapFont.html) [code](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g2d/BitmapFont.java)

## Tools for Creating Bitmaps

[[Hiero]] - a utility for converting a system font to a bitmap

[Glyph Designer](http://71squared.com/en/glyphdesigner) - a commercial bitmap font tool with a wide variety of options for shadows, gradients, stroke, etc.

[Glyphite.com](http://www.glyphite.com) - a browser-based Bitmap font generator that can create detailed Bitmap fonts in seconds and export them in most major formats.

## Other Tools

[FreeTypeFontGenerator](http://www.badlogicgames.com/wordpress/?p=2300) - generating bitmaps for fonts instead of supplying a pre-rendered bitmap made by utilities like Hiero

[[Distance field fonts]] - useful for scaling/rotating fonts without ugly artifacts

[gdx-smart-font](https://github.com/jrenner/gdx-smart-font) - unofficial libgdx addon for automatically generating and caching bitmap fonts based on screen size. (Uses FreeTypeFontGenerator)