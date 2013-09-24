 * [TexturePacker](#TexturePacker)
   * [Running TexturePacker2](#Running_TexturePacker2)
   * [Directory structure](#Directory_structure)
   * [Configuration](#Configuration)
   * [Settings](#Settings)
   * [NinePatches](#NinePatches)
   * [indexes](#Image_indexes)
   * [Packing](#Packing)
   * [Automatic packing](#Automatic_packing)
 * [TextureAtlas](#TextureAtlas)

# <a id="TexturePacker"></a>TexturePacker #

In OpenGL, a texture is bound, some drawing is done, another texture is bound, more drawing is done, etc. Binding the texture is relatively expensive, so it is ideal to store many smaller images on a larger image, bind the larger texture once, then draw portions of it many times. libgdx has a `TexturePacker2` class which is a command line application that packs many smaller images on to larger images. It stores the locations of the smaller images so they are easily referenced by name in your application using the `TextureAtlas` class.

TexturePacker is based on [the maximal rectangles algorithm](http://clb.demon.fi/projects/even-more-rectangle-bin-packing). It also does brute force, packing various ways at various sizes and then choosing the most efficient result.

_Note the latest version of the libgdx texture packer is the class `TexturePacker2`. The `TexturePacker` class is deprecated._

## <a id="Running_TexturePacker2"></a>Running TexturePacker2 ##

The `TexturePacker2` class is in the gdx-tools project. It can be run from source via Eclipse:

```java
import com.badlogic.gdx.tools.imagepacker.TexturePacker2;
public class MyPacker {
	public static void main (String[] args) throws Exception {
		TexturePacker2.process(inputDir, outputDir, packFileName);
	}
}
```

It can also be run from the [nightly build](http://libgdx.badlogicgames.com/nightlies/):

```java
java -cp gdx.jar;extensions/gdx-tools/gdx-tools.jar com.badlogic.gdx.tools.imagepacker.TexturePacker2 inputDir [outputDir] [packFileName]
```

Note that TexturePacker2 runs significantly faster with Java 1.7+, especially when packing hundreds of input images.

## <a id="Directory_structure"></a>Directory structure ##

TexturePacker can pack all images for an application in one shot. Given a directory, it recursively scans for image files. For each directory of images TexturePacker encounters, it packs the images on to a larger texture, called a page. If the images in a directory don't fit on the max size of a single page, multiple pages will be used.

Images in the same directory go on the same set of pages. If all images fit on a single page, no subdirectories should be used because with one page the app will only ever perform one texture bind. Otherwise, subdirectories can be used to segregate related images to minimize texture binds. Eg, an application may want to place all the "game" images in a separate directory from the "pause menu" images, since these two sets of images are drawn serially: all the game images are drawn (one bind), then the pause menu is drawn on top (another bind). If the images were in a single directory that resulted in more than one page, each page could contain a mix of game and pause menu images, which would cause unnecessary texture binds.

Subdirectories are also useful to group images with related texture settings. Settings like format (RGBA, RGB, etc) and filter (nearest, linear, etc) are per texture. Images that need different per texture settings need to go on separate pages, so should be placed in separate subdirectories.

To use subdirectories for organization without TexturePacker outputting a set of pages for each subdirectory, see the `combineSubdirectories` setting.

To avoid subdirectory paths being used in image names in the atlas file, see the `flattenPaths` setting.

## <a id="Configuration"></a>Configuration ##

Each directory may contain a "pack.json" file, which is a JSON representation of the TexturePacker2.Settings class. Each subdirectory inherits all the settings from its parent directory. Any settings set in the subdirectory override those set in the parent directory.

Below is a JSON example with every available setting and the default value for each. All settings do not need to be specified, any or all may be omitted. If a setting is not specified for a directory or any parent directory, the default value is used.

```
{
	pot: true,
	paddingX: 2,
	paddingY: 2,
	bleed: true,
	edgePadding: true,
	rotation: false,
	minWidth: 16,
	minHeight: 16,
	maxWidth: 1024,
	maxHeight: 1024,
	stripWhitespaceX: false,
	stripWhitespaceY: false,
	alphaThreshold: 0,
	filterMin: Nearest,
	filterMag: Nearest,
	wrapX: ClampToEdge,
	wrapY: ClampToEdge,
	format: RGBA8888,
	alias: true,
	outputFormat: png,
	jpegQuality: 0.9,
	ignoreBlankImages: true,
	fast: false,
	debug: false,
	combineSubdirectories: false,
	flattenPaths: false,
	premultiplyAlpha: false,
	useIndexes: true
}
```

Note that this is libgdx's "minimal" JSON format, so double quotes are optional in most cases.

## <a id="Settings"></a>Settings ##

| *Field* | *Description* | *Default* |
|:-------:|:------------- |:---------:|
| `pot` | If true, output pages will have power of two dimensions (required for GL1). | true |
| `paddingX` | The number of pixels between packed images on the x-axis. | 2 |
| `paddingY` | The number of pixels between packed images on the y-axis. | 2 |
| `bleed` | If true, RGB values for transparent pixels are set based on the RGB values of the nearest non-transparent pixels. This prevents filtering artifacts when RGB values are sampled for transparent pixels. | true |
| `edgePadding` | If true, half of the `paddingX` and `paddingY` will be used around the edges of the packed texture. | true |
| `rotate` | If true, TexturePacker will attempt more efficient packing by rotating images 90 degrees. Applications must take special care to draw these regions properly. | false |
| `minWidth` | The minimum width of output pages. | 16 |
| `minHeight` | The minimum height of output pages. | 16 |
| `maxWidth` | The maximum width of output pages. 1024 is safe for all devices. Extremely old devices may have degraded performance over 512. | 1024 |
| `maxHeight` | The maximum height of output pages. 1024 is safe for all devices. Extremely old devices may have degraded performance over 512. | 1024 |
| `stripWhitespaceX` | If true, blank pixels on the left and right edges of input images will be removed. Applications must take special care to draw these regions properly. | false |
| `stripWhitespaceY` | If true, blank pixels on the top and bottom edges of input images will be removed. Applications must take special care to draw these regions properly. | false |
| `alphaThreshold` | From 0 to 255. Alpha values below this are treated as zero when whitespace is stripped. | 0 |
| `filterMin` | The minification filter for the texture. | Nearest |
| `filterMag` | The magnification filter for the texture. | Nearest |
| `wrapX` | The wrap setting in the x direction for the texture. | ClampToEdge |
| `wrapY` | The wrap setting in the y direction for the texture. | ClampToEdge |
| `format` | The format the texture will use in-memory. | RGBA8888 |
| `alias` | If true, two images that are pixel for pixel the same will only be packed once. | true |
| `outputFormat` | The image type for output pages, "png" or "jpg". | png |
| `jpegQuality` | From 0 to 1. The quality setting if `outputFormat` is "jpg". | 0.9 |
| `ignoreBlankImages` | If true, texture packer won't add regions for completely blank images. | true |
| `fast` | If true, the texture packer will not pack as efficiently but will execute much faster. | false |
| `debug` | If true, lines are drawn on the output pages to show the packed image bounds. | false |
| `combineSubdirectories` | If true, the directory containing the settings file and all subdirectories are packed as if they were in the same directory. Any settings files in the subdirectories are ignored. | false |
| `flattenPaths` | If true, only image file names are used in the atlas, any subdirectories are not used. | false |
| `premultiplyAlpha` | If true, the RGB will be multiplied by the alpha. See [here](http://blogs.msdn.com/b/shawnhar/archive/2009/11/06/premultiplied-alpha.aspx) for more information. | false |
| `useIndexes` | If false, image names are used without stripping any image index suffix. | false |

## <a id="NinePatches"></a>NinePatches ##

If an image file name ends with ".9" just before the file extension, it is considered a ninepatch. NinePatch images can be created using [this tool](http://developer.android.com/tools/help/draw9patch.html). The image must have a 1px transparent border. The upper and left edge may optionally have one contiguous line of black pixels which denote the split information, ie what part of the ninepatch will stretch. The bottom and right edge may optionally have one contiguous line of black pixels which denote the padding information, ie how content on top of the NinePatch should be inset. When this image is packed, the 1px border is removed and the split and padding information stored in the pack file. `TextureAtlas` allows an instance of NinePatch to created for the region using the split information.

## <a id="Image_indexes"></a>Image indexes ##

If an image file name ends with underscore and then a number (eg animation_23.png), the number is considered the "index" and is stored separately. The image name is stored without the underscore and index. `TextureAtlas` allows a list of all images with the same name to be retrieved, ordered by index. This makes it easy to pack animations without losing the order of the frames.

## <a id="Packing"></a>Packing ##

The TexturePackage2 class is in `gdx-tools.jar`, which is in the extensions directory of the nightlies/releases zip files. You only need the TexturePacker as a tool to process your image files for your application, you don't need it as a dependency to run your application. To run the packer you need both `gdx.jar` and `gdx-tools.jar`.

```
java -classpath gdx.jar;gdx-tools.jar com.badlogic.gdx.tools.imagepacker.TexturePacker2 inputDir outputDir packFileName
```

Be sure to specify the `TexturePacker2` class, not the deprecated `TexturePacker` class. `inputDir` is the root directory containing the images. `outputDir` is the output directory where the packed images will be placed. `packFileName` is the name prefix used for the output files.

If `outputDir` is omitted, files will be placed in a new directory that is a sibling to `inputDir` with the suffix "-packed". If `packFileName` is omitted, "pack" is used.

While texture packing is intended to be a fully automated process, there has also been a nice UI contributed by Obli: [TexturePacker GUI](https://code.google.com/p/libgdx-texturepacker-gui/). There is also a commercial product at [texturepacker.com](http://www.codeandweb.com/texturepacker) that has even more features than the libgdx texture packer, such as automatic downsizing.

## <a id="Automatic_packing"></a>Automatic packing ##

During development it can be convenient to have the desktop application run TexturePacker2 before starting the game:

```java
public class DesktopGame {
	public static void main (String[] args) throws IOException {
		Settings settings = new Settings();
		settings.maxWidth = 512;
		settings.maxHeight = 512;
		TexturePacker2.process(settings, "../images", "../game-android/assets", "game");

		new LwjglApplication(new Game(), "Game", 320, 480, false);
	}
}
```

Each time the game is run, all the images are packed. This can be especially convenient when giving a build to an artist, who can then try out new images without even knowing the game is using packed images.

_Note: When loading files from the classpath, Eclipse usually will not reflect changes to files that are updated externally. The project with the changed files must be manually refreshed in Eclipse. During development files can be loaded through the filesystem instead, where this is not an issue._

# <a id="TextureAtlas"></a>TextureAtlas #

The TexturePacker output is a directory of page images and a text file that describes all the images packed on the pages. This shows how to use the images in an application:

```java
TextureAtlas atlas;
atlas = new TextureAtlas(Gdx.files.internal("packedimages/pack.atlas"));
AtlasRegion region = atlas.findRegion("imagename");
Sprite sprite = atlas.createSprite("otherimagename");
NinePatch patch = atlas.createPatch("patchimagename");
```

TextureAtlas reads the pack file and loads all the page images. TextureAtlas.AtlasRegions can be retrieved, which are TextureRegions that provides extra information about the packed image, such as the frame index or any whitespace that was stripped. Sprites and NinePatches can also be created. If whitespace was stripped, the created Sprite will actually be a TextureAtlas.AtlasSprite, which allows the sprite to be used (mostly) as if whitespace was never stripped.

Note that `findRegion` is not very fast, so the value returned should be stored rather than calling this method each frame. Also note that createSprite and createNinePatch allocate a new instance.

TextureAtlas holds on to all the page textures. Disposing the TextureAtlas will dispose all the page textures.