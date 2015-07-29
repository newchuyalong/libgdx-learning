# No Op: not doing this, we already have another article...


#  Why would I want to use the AssetManager? 
If your game is very simple, and loading resources doesn't take a lot of time you don't want to use the AssetManager. In all other cases I'd recommend using it due to the following nice behaviours:


  * Loading of most resources is done asynchronously, so you can display a reactive loading screen while things load
  * Assets are reference counted. If two assets A and B both depend on another asset C, C won't be disposed until A, B and C have been disposed. This also means that if you load an asset multiple times, it will actually be shared and only take up memory once!
  * A single place to store all your assets.
  * Allows to transparently implement things like caches (see FileHandleResolver below)

Still with me? Then read on.

#  Creating an AssetManager 
This part is rather simple:

```
AssetManager manager = new AssetManager();
```

This sets up a standard AssetManager, with all the loaders libgdx has in store at the moment. Let's see how the loading mechanism works.

#  Loading Assets 
To load assets, the AssetManager needs to know how to load a specific type of asset. This functionality is implemented via AssetLoaders. There's two variants, SynchronourAssetLoader and AsynchronousAssetLoader. The former loads everything on the rendering thread, the later loads parts of the asset on another thread, e.g. the Pixmap needed for a Texture, and then loads the OpenGL dependent part on the rendering thread. The following resources can be loaded out of the box with the AssetManager as constructed above.


  * Pixmaps via PixmapLoader
  * Textures via TextureLoader
  * BitmapFonts via BitmapFontLoader
  * TextureAtlases via TextureAtlasLoader
  * TiledAtlases via TiledAtlasLoader
  * TileMapRenderers via TileMapRendererLoader
  * Music instances via MusicLoader
  * Sound instances via SoundLoader

Loading a specific asset is simple:

```
manager.load("data/mytexture.png", Texture.class);
manager.load("data/myfont.fnt", BitmapFont.class);
manager.load("data/mymusic.ogg", Music.class);
```

These calls will enqueue those assets for loading. The assets will be loaded in the order we called the AssetManager#load() method. Some loaders allow you to also pass parameters to them via AssetManager#load(). Say we want to specify a non-default filter and mipmapping setting for loading a texture:

```
TextureParameter param = new TextureParameter();
param.minFilter = TextureFilter.Linear;
param.genMipMaps = true;
manager.load("data/mytexture.png", Texture.class, param);
```

Look into the loaders mentioned above to find out about their parameters.

So far we only queued assets to be loaded. The AssetManager does not yet load anything. To kick this off we have to call AssetManager#update() continuously, say in our ApplicationListener#render() method:

```
public MyAppListener implements ApplicationListener {

   public void render() {
      if(manager.update()) {
         // we are done loading, let's move to another screen!
      }

      // display loading information
      float progress = manager.getProgress()
      ... left to the reader ...
   }
}
```

As long as AssetManager#update() returns false you know it's still loading assets. To poll the concrete state of loading you can use AssetManager#getProgress(), which returns a number between 0 and 1 indicating the percentage of assets loaded so far. There are other methods in AssetManager that give you similar information, like AssetManager#getLoadedAssets() or AssetManager#getQueuedAssets(). <b>You have to call AssetManager#update() to keep loading!</b>

If you want to block and make sure all assets are loaded you can call:

```
manager.finishLoading();
```

This will block until all the assets that have been queued are actually done loading. Kinda defeats the purpose of asynchronous loading, but sometimes one might need it (e.g. loading the assets needed to display the loading screen itself).

#  Getting Assets 
That's again easy:

```
Texture tex = manager.get("data/mytexture.png", Texture.class);
BitmapFont font = manager.get("data/myfont.fnt", BitmapFont.class);
```

This of course assumes that those assets have been successfully loaded. If we want to poll whether a specific asset has been loaded we can do the following:

```
if(manager.isLoaded("data/mytexture.png")) {
   // texture is available, let's fetch it and do do something interesting
   Texture tex = manager.get("data/mytexture.png", Texture.class);
}
```

#  Disposing Assets 
Easy again, and here you can see the real power of the AssetManager:

```
manager.unload("data/myfont.fnt");
```

If that font references a Texture that you loaded manually before, the texture won't get destroyed! It will be reference counted, getting one reference from the bitmap font and another from itself. As long as this count is not zero, the texture won't be disposed. 

*Assets managed via the AssetManager shouldn't be disposed manually, instead call AssetManager#unload()!*

If you want to get rid of all assets at once you can call:

```
manager.clear();
```

or 

```
manager.dispose();
```

Both will dispose all currently loaded assets and remove any queued and not yet loaded assets. The AssetManager#dispose() method will also kill the AssetManager itself. After a call to this method you should not use the manager anymore.

And that's pretty much everything there is. Now for the nitty-gritty parts.

#  I only supply Strings, where does the AssetManager load the assets from? 
Every loader has a reference to a FileHandleResolver. That's a simple interface looking like this:

```
public interface FileHandleResolver {
   public FileHandle resolve(String file);
}
```

By default, every loader uses an InternalFileHandleResolver. That will return a FileHandle pointing at an internal file (just like Gdx.files.internal("data/mytexture.png"). You can write your own resolvers! Look into the assets/loaders/resolvers package for more FileHandleResolver implementation. One use case for this would be a caching system, where you check if you have a newer version downloaded to the external storage first, and fall back to the internal storage if it's not available. The possibilities are endless.

You can set the FileHandleResolver to be used via the second constructor of AssetManager:

```
AssetManager manager = new AssetManager(new ExternalFileHandleResolver());
```

This will make sure all default loaders listed above will use that loader.

#  Writing your own Loaders 
I can't anticipate which other types of resources you want to load, so at some point you might want to write your own loaders. There are two interfaces called SynchronousAssetLoader and AsynchronousAssetLoader you can implement. Use the former if your asset type is fast to load, use the later if you want your loading screen to be responsive. I suggest basing your loader on the code of one of the loaders listed above. Look into MusicLoader for a simple SynchronousAssetLoader, look into PixmapLoader for a simple AsynchronousAssetLoader. BitmapFontLoader is a good example of an asynchronous loader that also has depdendencies that need to be loaded before the actual asset can be loaded (in that case it's the texture storing the glyphs). Again, you can do pretty much anything with this.

Once you are done writing your loader, tell the AssetManager about it:

```
manager.setLoader(MyAssetClass.class, new MyAssetLoader(new InternalFileHandleResolver()));
manager.load("data/myasset.mas", MyAssetClass.class);
```

#  Resuming with a Loading Screen 
On Android your app can be paused and resumed. Managed OpenGL resources like Textures need to be reloaded in that case, which can take a bit of time. If you want to display a loading screen on resume, you can do the following after you created your AssetManager.

```
Texture.setAssetManager(manager);
```

In your ApplicationListener#resume() method you can then switch to your loading screen and call AssetManager#update() again until everything is back to normal.

If you don't set the AssetManager as shown in the last snippet, the usual managed texture mechanism will kick in, so you don't have to worry about anything.

And this concludes the long awaited article on the AssetManager. 