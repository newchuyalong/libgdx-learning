This example shows how to take a screenshot and save it in PNG format. It even adds a suffix when multiple Screenshots are taken.

```java
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

	private static Pixmap getScreenshot(int x, int y, int w, int h,	boolean yDown){
		final Pixmap pixmap = ScreenUtils.getFrameBufferPixmap(x, y, w, h);

		if (yDown) {
			// Flip the pixmap upside down
			ByteBuffer pixels = pixmap.getPixels();
			int numBytes = w * h * 4;
			byte[] lines = new byte[numBytes];
			int numBytesPerLine = w * 4;
			for (int i = 0; i < h; i++) {
				pixels.position((h - i - 1) * numBytesPerLine);
				pixels.get(lines, i * numBytesPerLine, numBytesPerLine);
			}
			pixels.clear();
			pixels.put(lines);
			pixels.clear();
		}

		return pixmap;
	}
}
```

##Using Screenshots to Create Videos

Screenshots can also be used to create lossless videos by taking a screenshot every frame and using external software to convert the PNGs to a video.

[Example Video](http://www.youtube.com/watch?v=NsblslUDWqU)

On Linux, this can be done with ffmpeg.

```bash
ffmpeg -i screenshot%d.png -r 60 -sameq -vcodec mpeg4 output.avi
```

To increase performance while taking screenshots, screenshots can be stored in memory (an Array or other collection) and written in a large batch.  This obviously requires large amounts of available memory, depending on the size of the PNGs and the number of shots taken per batch.  It is a good method for taking gameplay videos of only a few seconds in length without slowing down the game too much with File IO operations.