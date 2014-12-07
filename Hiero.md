Hiero is a bitmap font packing tool. It saves in the [Angel Code font](http://www.angelcode.com/products/bmfont/) format, which can be used by [[BitmapFont | Bitmap Fonts]] in libgdx applications.

## Running Hiero ##

You can download the .jar file [here](https://code.google.com/p/libgdx/downloads/detail?name=hiero.jar&can=2&q=) (9MB). Make it executable (if you are on a UNIX-like operating system) and then run it.

If you are using Gradle and you added "Tools" extension to your project, you can easily run Hiero from your IDE, otherwise look at Downloading Hiero.

Example for IntelliJ IDEA: Go to the Hiero class, right click and select `Run Hiero.main()`. On the `Run configurations` popup that will appear, select the `Desktop` module, and click `Run`.

Hiero can also be run from source or from the [nightly build](http://libgdx.badlogicgames.com/nightlies/). Therefore navigate into the folder where libGDX is installed, especially where the gdx.jar is:

On Windows
```
java -cp gdx.jar;gdx-natives.jar;gdx-backend-lwjgl.jar;gdx-backend-lwjgl-natives.jar;extensions\gdx-tools\gdx-tools.jar com.badlogic.gdx.tools.hiero.Hiero
```

On Linux/OS X
```
java -cp gdx.jar:gdx-natives.jar:gdx-backend-lwjgl.jar:gdx-backend-lwjgl-natives.jar:extensions/gdx-tools/gdx-tools.jar com.badlogic.gdx.tools.hiero.Hiero
```

It can also be run via JWS, though this is a slightly out of date version:
[Hiero Java WebStart](http://wiki.libgdx.googlecode.com/git/jws/hiero.jnlp). But this version is not able to create [distant field fonts](https://github.com/libgdx/libgdx/wiki/Distance-field-fonts), therefore use a newer version on you own computer as described above.

## Rasterization ##

Hiero rasterizes TTF fonts using Java's font rendering. Kerning is supported. The output is usually of reasonable quality and allows various effects to be applied, such as a drop shadow, outline, etc. At small sizes, the output can be poor for some fonts.

## Alternatives ##

### Glyph Designer ###

[Glyph Designer](http://www.71squared.com/glyphdesigner) is a commercial bitmap font tool designed specifically for Mac. It allows you to create beautifully styled text with custom backgrounds, gradient fills, gradient strokes & shadows. The command line interface allows you to export multi-lingual character sets and target multiple device profiles. At time of writing Glyph Designer is priced at $39.99

### BMFont ###

The [BMFont](http://www.angelcode.com/products/bmfont/) tool uses FreeType and can produce better results at small sizes. It can also do supersampling, providing smoother glyphs. BMFont does not support effects like drop-shadows or outlines, but glyphs can be output with padding and effects applied with Paint.NET, Photoshop, etc. Typical export settings are [here](http://wiki.libgdx.googlecode.com/git/img/bmfont-export.png).

BMFont is Windows only but can be run using [Wine](http://www.winehq.org/). There are reports that it hangs if the space character is exported. The space character can be added manually, eg:
```
char id=32   x=0   y=0    width=0     height=0     xoffset=0    yoffset=0    xadvance=3     page=0  chnl=15
```
Change the xadvance as needed, this is the number of pixels for a space character.

### TWL Theme Editor ###

The [TWL](http://twl.l33tlabs.org/) Theme Editor has a font tool that also uses !FreeType, though it doesn't support the super-sampling. [Theme Editor JWS](http://twl.l33tlabs.org/themer/themer.jnlp).

### gdx-fontpack ###

The [gdx-fontpack](https://github.com/mattdesl/gdx-fontpack) tool also uses FreeType. It doesn't yet support super-sampling.

### FontPacker ###

The [FontPacker](http://www.java-gaming.org/topics/fontpacker-pack-truetype-fonts-into-your-game/30219/view.html) tool is written in C# and uses .NET's TextRenderer, FontFamily, and Graphics classes to render.

### ShoeBox ###

[ShoeBox](http://renderhjs.net/shoebox/) has a tool for creating Angel Code fonts.

### JME ###

jMonkeyEngine has an Angel Code [font tool](http://jmonkeyengine.org/groups/jmonkeyplatform/forum/topic/font-creator-for-jmp/), though it looks simplistic and most likely uses Java's font rendering, same as Hiero.

### bmglyph ###

The [bmglyph](http://www.bmglyph.com//) tool is for OSX only and hasn't been evaluated.

### Littera ###

http://kvazars.com/littera/