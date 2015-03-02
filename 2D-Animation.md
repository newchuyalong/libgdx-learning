2D Animation is the technique used to create the illusion of movement using static images. This article describes how to create animations with libGdx.

## Details ##

An animation consists of multiple frames which are shown in a sequence at set intervals. An animation of a running man can be achieved by taking pictures of him while running and playing those images back infinitely.

The following image shows a complete  running cycle of a man. It is called a sprite sheet. Each box is a sprite and it is called a frame. To create the running animation, the sprites have to be drawn one after the other as time passes.

[[images/sprite-animation1.png]]

Depending on how fast the character is running, the time a frame is kept on the screen needs to be determined. The frame rate is the number of times we switch frames per second. Examining the sprite sheet, we can see that a complete running cycle consists of 30 frames (6 columns and 5 rows). If the character is to complete a cycle in a second, we need to show all the 30 frames in a second. That gives us the frame rate of 30 FPS (Frames Per Second). The clue is in the name.
Going further, it is easy to calculate the state time (frame time), which is how much time a frame will be displayed before the next one replaces it. 1 second / 30 = 0.033.

In other words, to have an animation at  30 FPS, the current frame needs to be changed every 0.033 second.

An animation is a very simple state machine. The running man has 30 states as per the sprite sheet. The numbered frames represent the states a running man goes through.

[[images/sprite-animation2.png]]

A state machine can’t be in 2 or more states at any given time. Working out the states is extremely easy.
When the character is in State 1, then the sprite associated with that state is drawn. 
It stays in that state for 0.033 second and once the time elapsed, it moves (transitions) to the next state, which is State 2.
This goes on until the last state/frame (30) is met.

Looping animation means, that when the animation reached the last frame, it jumps to the first frame and starts again.

Using animations in libGdx is extremely simple. 
One constraint regarding the size of the spritesheet needs to be remembered: when using OpenGL 1.x, the size of sprite sheet has to be power of two. (check Texture for more information).

The following code snippet will create an [Animation](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/g2d/Animation.html) [(code)](https://github.com/libgdx/libgdx/blob/master/gdx/src/com/badlogic/gdx/graphics/g2d/Animation.java) using the animation_sheet.png sprite-sheet and renders the animation to the screen. It is a very basic ApplicationListener. To see how to create one and what exactly it is, look [[here for textures|Spritebatch, textureregions, and sprites]].

```java
public class Animator implements ApplicationListener {

	private static final int        FRAME_COLS = 6;         // #1
	private static final int        FRAME_ROWS = 5;         // #2
	
	Animation                       walkAnimation;          // #3
	Texture                         walkSheet;              // #4
	TextureRegion[]                 walkFrames;             // #5
	SpriteBatch                     spriteBatch;            // #6
	TextureRegion                   currentFrame;           // #7
	
	float stateTime;                                        // #8
	
	@Override
	public void create() {
		walkSheet = new Texture(Gdx.files.internal("animation_sheet.png"));	// #9
		TextureRegion[][] tmp = TextureRegion.split(walkSheet, walkSheet.getWidth()/FRAME_COLS, walkSheet.getHeight()/FRAME_ROWS);				// #10
		walkFrames = new TextureRegion[FRAME_COLS * FRAME_ROWS];
		int index = 0;
		for (int i = 0; i < FRAME_ROWS; i++) {
			for (int j = 0; j < FRAME_COLS; j++) {
				walkFrames[index++] = tmp[i][j];
			}
		}
		walkAnimation = new Animation(0.025f, walkFrames);		// #11
		spriteBatch = new SpriteBatch();				// #12
		stateTime = 0f;							// #13
	}

	@Override
	public void render() {
		Gdx.gl.glClear(GL20.GL_COLOR_BUFFER_BIT | GL20.GL_DEPTH_BUFFER_BIT);						// #14
		stateTime += Gdx.graphics.getDeltaTime();			// #15
		currentFrame = walkAnimation.getKeyFrame(stateTime, true);	// #16
		spriteBatch.begin();
		spriteBatch.draw(currentFrame, 50, 50);				// #17
		spriteBatch.end();
	}
}
```


### The Setup ###

*#1* and *#2* defines constants representing how many sprites are laid out horizontally and vertically on the sheet. 

*Note*: The sprite-sheet contains frames of equal size and they are all aligned.

*#3* - Declaration of the walkAnimantion object which is libGdx’s implementation of animation.

*#4* - The Texture which will contain the whole sheet as a single image (texture).

*#5* - Declare walkFrames as an array of TextureRegion objects. This array will hold each frame (sprite) of the animation. The first element will hold the frame from the top left, the second will hold the one right to it and so on. When the last element in the row is reached, the next row follows with the left most element.

*#6* - The SpriteBatch is used to draw the texture onto the screen. 

*#7* - This variable will hold the current frame and this is the region which is drawn on each render call.

*#8* - The stateTime is the number of seconds elapsed from the start of the animation. This is used to determine the state of the animation. It is a simple accumulator based on which the animation knows when to change to the next state. 

For example, if the animation is a 30FPS one, then the state change needs to happen at each 33.3 milliseconds. If the update happens at 10 milliseconds, the stateTime accumulates the elapsed time and the animation will change to the next state (frame) at the 4th update.

*#9* - Creates a texture from animation_sheet.png which is placed in the assets directory of the project (check [[how to set up a project | Project setup, running & debugging]])

*#10* - *#11* - Using the split convenience method on the texture, we obtain a two dimensional array of the frames from the texture. Bear in mind that this works only if the frames are of equal size. Then with the help of a temporary variable, the walkFrames array is populated. This is necessary, as the Animation works with one dimensional arrays only. 

The walkFrames can be populated in any way that suits you, for example by loading each frame from different files, however this is not advisable because of performance issues.

*#11* - This is where the Animation is created. The first parameter tells the animation, how much time is allocated for each frame. This is expressed in seconds. The more frames an animation has, the smaller the time it will be and the smoother it will look. It consumes more memory though. 
The second parameter is the array containing all the frames. The first element is the first frame and so on.

*#12* - Initialises the SpriteBatch which will draw the frame.

*#13* - Resets the stateTime to 0. It will start accumulating the time each render call.

### The render method ###

*#14* - Clears the screen each frame.

*#15* - Adds the time elapsed since the last render to the stateTime.

*#16* - Obtains the current frame. This is given by the animation for the current time. The second variable is the looping. Passing in true, we tell the animation to restart after it reaches the last frame.

*#17* - Renders the current frame onto the screen using the super awesome SpriteBatch at 50,50.

Running the above code snippet, will produce a nice smooth animation of a man running.

[[images/sprite-animation3.png]]

Creating an animation is extremely simple by using the following constructor.

| Method signature | Description |
|:-------------------|:--------------|
| `Animation (float frameDuration, TextureRegion... keyFrames)` | The first parameter is the frame time and the second is an array of regions (frames) making up the animation|

## Best practices ##
 * Pack frames into one texture to optimize rendering
 * Settle for a reasonable number of frames depending on the game type. For a retro arcade style, 4 frames suffice, while more realistic looking movements require more frames.

## Assets ##

Get the sprite-sheet [here](images/sprite-animation4.png).