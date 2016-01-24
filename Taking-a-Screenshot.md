```java
byte[] pixels = ScreenUtils.getFrameBufferPixels(0, 0, Gdx.graphics.getBackbufferWidth(), Gdx.graphics.getBackbufferHeight(), true);

Pixmap pixmap = new Pixmap(Gdx.graphics.getBackbufferWidth(), Gdx.graphics.getBackbufferHeight(), Pixmap.Format.RGBA8888);
BufferUtils.copy(pixels, 0, pixmap.getPixels(), pixels.length);
PixmapIO.writePNG(Gdx.files.external("mypixmap.png", pixmap);
pixmap.dispose();
```