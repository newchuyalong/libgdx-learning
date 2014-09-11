For some games like first person shooters, it is often necessary to catch the cursor so it stays in the center of the screen, and only use position deltas to rotate the camera. Other times we might want to position the cursor manually. Both things can be done as follows:

```java
Gdx.input.setCursorCatched(true);
Gdx.input.setCursorPosition(x, y);
```

Note that catching the cursor only works reliably in the Lwjgl back-end. The Jogl backend uses the AWT/Swing Robot class which has a few quirks.

Cursor catching and positioning is only available on the desktop.

Similarly, changing the cursor image is available on the desktop and can be done as follows:

```java
Gdx.input.setCursorImage(new Pixmap(Gdx.files.internal("cursor.png")), hotspotX, hotspotY);
```