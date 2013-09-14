# Full-screen & vSync #

## Checking current setting ##

To see if full-screen is currently enabled, simply use
```java
boolean fullscreen = Gdx.graphics.isFullscreen();
```

## Set full-screen and vSync ##

To set full-screen and/or vSync on start-up, specify the corresponding flags in your `LwjglApplicationConfiguration`:
```java
public static void main(String[] args) {
	LwjglApplicationConfiguration cfg = new LwjglApplicationConfiguration();
	cfg.width = 1280;
	cfg.height = 720;

	// fullscreen
	cfg.fullscreen = true;
	// vSync
	cfg.vSyncEnabled = true;

	new LwjglApplication(new YourApplicationListener(), cfg);
}
```

Full-screen cannot be enabled by setting a flag, you have to set a new DisplayMode.
To enable/disable full-screen at any given time, use (working on desktop only)
```java
// set resolution to HD ready (1280 x 720) and set full-screen to true
Gdx.graphics.setDisplayMode(1280, 720, true);

// set resolution to default and set full-screen to true
Gdx.graphics.setDisplayMode(Gdx.graphics.getDesktopDisplayMode().width, Gdx.graphics.getDesktopDisplayMode().height, true);
```

To enable/disable vSync at any given time, use
```java
Gdx.graphics.setVSync(true);
```