#Overview#
libgdx-utils is a small support library for games and applications powered by LibGDX. It provides tools for various problems to save you time and effort.

libgdx-utils is (hosted on bitbucket)[https://bitbucket.org/dermetfan/libgdx-utils/wiki/Home]

#Api-Referance#
# libgdx-utils #

- [Javadoc](http://dermetfan.net/libgdx-utils/javadoc)

## Graphics ##

- [**AnimatedSprite**](net.dermetfan.gdx.graphics.g2d.AnimatedSprite) - an animated Sprite using LibGDX's `Animation`

## Math ##

- [Noise](net.dermetfan.utils.math.Noise) - provides noise algorithms like diamond-square and midpoint-displacement
- MathUtils - provides math utility methods
- GeometryUtils - provides some methods for geometric calculations

## Maps ##

- [MapUtils](net.dermetfan.gdx.maps.MapUtils) - provides useful methods when dealing with maps
- [TileAnimator](net.dermetfan.gdx.maps.TileAnimator) - animates tiles in a tile map
- [**TmxMapWriter**](net.dermetfan.gdx.maps.tiled.TmxMapWriter) - writes a Map to a TMX file

## Scene2D ##

- [FileChooser](net.dermetfan.gdx.scenes.scene2d.ui.FileChooser) - a super class for various file chooser implementations
- [**ListFileChooser**](net.dermetfan.gdx.scenes.scene2d.ui.ListFileChooser) - browse the file system and choose one more more files
- [**TreeFileChooser**](net.dermetfan.gdx.scenes.scene2d.ui.TreeFileChooser) - choose one or more files from a directory tree
- CircularGroup - a WidgetGroup that aligns its children in a circle, allows advanced configuration
- [**Popup**](net.dermetfan.gdx.scenes.scene2d.ui.Popup) - flexible popup system for things like tooltips, context menus and menu bars
- [JigsawPuzzle](net.dermetfan.gdx.scenes.scene2d.ui.JigsawPuzzle) - a complete jigsaw puzzle mini game

## Miscellaneous ##

- [**AnnotationAssetManager**](net.dermetfan.gdx.assets.AnnotationAssetManager) - an `AssetManager` subclass loading assets from annotated fields
- [CutsceneManager](net.dermetfan.gdx.CutsceneManager) - a cutscene interface and manager
- [Typewriter](net.dermetfan.gdx.Typewriter) - "writes" a `CharSequence` like a typewriter with cursor using a [CharSequenceInterpolator](net.dermetfan.gdx.CharSequenceInterpolator) and an [Appender](net.dermetfan.utils.Appender)
- [Appender](net.dermetfan.utils.Appender) - appends different `CharSequence`s to a `CharSequence` based on time and duration
- [CharSequenceInterpolator](net.dermetfan.gdx.CharSequenceInterpolator) - interpolates the length of a subSequence of a `CharSequence` by time and chars per second using the `Interpolation` class from LibGDX
- ArrayUtils - utility methods for arrays like boxing and selecting
- Function - minimalistic rip-off of Java 8's `Function`
- Pair - a generic key - value pair

