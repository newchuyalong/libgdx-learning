This example shows how to take a screenshot and save it in PNG format. It even adds a suffix when multiple Screenshots are taken.

```
public class ScreenshotFactory {

	private static int counter = 1;
	public static void saveScreenshot(){
		try{
			FileHandle fh;
			do{
				fh = new FileHandle("screenshot" + counter++ + ".png");
			}while (fh.exists());
			Pixmap pixmap = getScreenshot(0, 0, Gdx.graphics.getWidth(), Gdx.graphics.getHeight(), false);
			PixmapIO.writePNG(fh, pixmap);
			pixmap.dispose();
		}catch (Exception e){			
		}
	}

	private static Pixmap getScreenshot(int x, int y, int w, int h,	boolean flipY){
		Gdx.gl.glPixelStorei(GL10.GL_PACK_ALIGNMENT, 1);

		final Pixmap pixmap = new Pixmap(w, h, Format.RGBA8888);
		ByteBuffer pixels = pixmap.getPixels();
		Gdx.gl.glReadPixels(x, y, w, h, GL10.GL_RGBA, GL10.GL_UNSIGNED_BYTE, pixels);

		final int numBytes = w * h * 4;
		byte[] lines = new byte[numBytes];
		if (flipY){
			pixels.clear();
			pixels.get(lines);
		}else{
			final int numBytesPerLine = w * 4;
			for (int i = 0; i < h; i++){
				pixels.position((h - i - 1) * numBytesPerLine);
				pixels.get(lines, i * numBytesPerLine, numBytesPerLine);
			}
			pixels.clear();
			pixels.put(lines);
		}
		return pixmap;
	}
}
```