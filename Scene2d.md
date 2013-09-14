## Overview ##

scene2d is a 2D scene graph for building applications and UIs using a hierarchy of actors. It provides the following features:

 * Rotation and scale of a group is applied to all child actors. Child actors always work in their own coordinate system, parent transformations are applied transparently.

 * Simplified 2D drawing via [[SpriteBatch | SpriteBatch, TextureRegions, and Sprite]]. Each actor draws in its own un-rotated and unscaled coordinate system where 0,0 is the bottom left corner of the actor.

 * Hit detection of rotated and scaled actors. Each actor determines if it is hit using its own un-rotated and unscaled coordinate system.

 * Routing of input and other events to the appropriate actor. The event system is flexible, enabling parent actors to handle events before or after children.

 * Action system for easy manipulation of actors over time. Actions can be chained and combined for complex effects.

scene2d is well equipped for laying out, drawing, and handling input for game menus, HUD overlays, tools, and other UIs. The [[scene2d.ui]] package provides many actors and other utilities specifically for building UIs.

Scene graphs have the drawback that they couple model and view. Actors store data that is often considered model data in games, such as their size and position. Actors are also the view, as they know how to draw themselves. This coupling makes MVC separation difficult. When used solely for UIs or for apps that don't care about MVC, the coupling is not an issue.

scene2d has three class at its core. The Actor class is a node in the graph which has a position, rectangular size, origin, scale, rotation, and color. The Group class is an actor that may have child actors. The Stage class has a camera, SpriteBatch, and a root group and handles drawing the actors and distributing input events.

== Stage ==

Stage is an InputProcessor. When it receives input events, it fires them on the appropriate actors. If the stage is being used as a UI on top of other content (eg, a HUD), an InputMultiplexer can be used to first give the stage a chance to handle an event. If an actor in the stage handles an event, stage's InputProcessor methods will return true, indicating the event has been handled and should not continue on to the next InputProcessor.

Stage has an `act` method that takes a delta time since last frame. This causes the `act` method on every actor in the scene to be called, allowing the actors to take some action based on time. By default, the Actor `act` method updates all actions on the actor. Calling `act` on the stage is optional, but actor actions and enter/exit events will not occur if it is omitted.

### Viewport ###

The stage viewport is setup using the `setViewport` method.

Stage has a viewport position and size which describes what part of the screen the stage will use. If `glViewport` is not used then the viewport position and size can be omitted when calling `setViewport`. This will cause the stage to use the entire screen as the viewport.

Stage also has a size. This describes how many units wide and tall the stage is when drawn over the whole viewport. If the viewport matches the screen resolution, then 1 unit in stage coordinates will correspond to 1 pixel. If the viewport is bigger or smaller than the screen resolution, the stage will be scaled up or down to fill the screen.

`setViewport` has a parameter named `keepAspectRatio` which only has an effect when the stage size and viewport size aspect ratio differ. If false, the stage is stretched to fill the viewport, which may distort the aspect ratio. If true, the stage is first scaled to fit the viewport in the longest dimension. Next the shorter dimension is lengthened to fill the viewport, which keeps the aspect ratio from changing.

If running where the application window can be resized (eg, on the desktop), the stage's viewport should be set when the application window is resized.

Here is an example of the most basic scene2d application with no actors, using pixel stage coordinates. Each unit in the stage corresponds to 1 pixel. Here the boolean passed to `setViewport` has no effect because the aspect ratio of the stage and viewport are the same. Note that the 3 parameter `setViewport` is called which does not specify a viewport position and size, so the entire screen is used as the viewport.

```java
private Stage stage;

public void create () {
	stage = new Stage();
	Gdx.input.setInputProcessor(stage);
}

public void resize (int width, int height) {
	stage.setViewport(width, height, true);
}

public void render () {
	Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	stage.act(Gdx.graphics.getDeltaTime());
	stage.draw();
}

public void dispose() {
	stage.dispose();
}
```

Here is an example of using a fixed stage size. The stage will be stretched to the viewport, potentially stretching the stage's aspect ratio.

```java
public void resize (int width, int height) {
	stage.setViewport(800, 480, false);
}
```

Here is another example of using a fixed stage size. The stage will be scaled to fit, and then the shorter dimension increased to fit the screen. The stage's aspect ratio will not change, but it may become longer in one direction. Note that 0,0 in the stage is still at the bottom left corner of the screen.

```java
public void resize (int width, int height) {
	stage.setViewport(800, 480, true);
}
```

Here is yet another example of using a fixed stage size, but with "black bars" on either side. As before, the stage is scaled to fit and then lengthened in one direction. The camera is then shifted so that 0,0 in the stage is half the amount the stage was lengthened. Note the stage still takes up the entire viewport, which is the whole screen, so drawing can occur within the "black bars".

```java
public void resize (int width, int height) {
	stage.setViewport(800, 480, true);
	stage.getCamera().translate(-stage.getGutterWidth(), -stage.getGutterHeight(), 0);
}
```

This example also uses a fixed stage size with "black bars" on either side, this time using `glViewport`. First the stage size of 800x480 is scaled to fit the screen size using the `Scaling` class. The result is used to configure `glViewport`, which changes which part of the screen OpenGL will use. Lastly, `setViewport` is passed the viewport position and size. The result is the same as the last example, the stage has "black bars" as necessary to keep the aspect ratio, but no drawing can occur outside the viewport.

```java
public void resize (int width, int height) {
	Vector2 size = Scaling.fit.apply(800, 480, width, height);
	int viewportX = (int)(width - size.x) / 2;
	int viewportY = (int)(height - size.y) / 2;
	int viewportWidth = (int)size.x;
	int viewportHeight = (int)size.y;
	Gdx.gl.glViewport(viewportX, viewportY, viewportWidth, viewportHeight);
	stage.setViewport(800, 480, true, viewportX, viewportY, viewportWidth, viewportHeight);
}
```

See [LetterBoxTest](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/LetterBoxTest.java), [LetterBoxTest2](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/LetterBoxTest2.java), and [LetterBoxTest3](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/LetterBoxTest3.java) for runnable examples of keeping an aspect ratio.

## Drawing ##

When `draw` is called on the stage, it calls draw on every actor in the stage. Actor's `draw` method can be overridden to perform drawing:

```java
public class MyActor extends Actor {
	TextureRegion region;

	public MyActor () {
		region = new TextureRegion(...);
	}

	public void draw (SpriteBatch batch, float parentAlpha) {
		Color color = getColor();
		batch.setColor(color.r, color.g, color.b, color.a * parentAlpha);
		batch.draw(region, getX(), getY(), getOriginX(), getOriginY(),
			getWidth(), getHeight(), getScaleX(), getScaleY(), getRotation());
	}
}
```

This `draw` method draws a region using the position, origin, size, scale, and rotation of the actor. The SpriteBatch passed to draw is configured to draw in the parent's coordinates, so 0,0 is the bottom left corner of the parent. This makes drawing simple, even if the parent is rotated and scaled. SpriteBatch begin has already been called. If the parentAlpha is combined with this actor's alpha as shown, child actors will be influenced by the parent's translucency. Note the color of the SpriteBatch may be changed by other actors and should be set by each actor before it draws.

If `setVisible(false)` is called on an actor, its draw method will not be called. It will also not receive input events.

If an actor needs to perform drawing differently, such as with a 
ShapeRenderer, the SpriteBatch should be ended and then begun again at the end of the method. Of course, this causes the batch to be flushed, so should be used judiciously. The transformation and projection matrices from the SpriteBatch can be used:

```java
private ShapeRenderer renderer;

public void draw (SpriteBatch batch, float parentAlpha) {
	batch.end();

	renderer.setProjectionMatrix(batch.getProjectionMatrix());
	renderer.setTransformMatrix(batch.getTransformMatrix());
	renderer.translate(getX(), getY(), 0);

	renderer.begin(ShapeType.Rectangle);
	renderer.rect(0, 0, getWidth(), getHeight());
	renderer.end();

	batch.begin();
}
```

### Group transform ###

When a group is rotated or scaled, the children draw as normal and the SpriteBatch's transform draws them correctly rotated or scaled. Before a group draws, the SpriteBatch is flushed so the transform can be set. This flush may become a performance bottleneck if there are many dozens of groups. If the actors in a group are not rotated or scaled, then `setTransform(false)` can be used for the group. When this is done, each child's position will be offset by the group's position for drawing, causing the children to appear in the correct location even though the SpriteBatch has not been transformed. This cannot be used for a group that has rotation or scale.

## Hit detection ##

The Actor `hit` method receives a point and returns the deepest actor at that point, or null if no actor was hit. Here is the default `hit` method:

```java
public Actor hit (float x, float y, boolean touchable) {
	if (touchable && getTouchable() != Touchable.enabled) return null;
	return x >= 0 && x < width && y >= 0 && y < height ? this : null;
}
```

The coordinates are given in the actor's coordinate system. This simply returns the actor if the point is inside the actor's bounds. More sophisticated checks could be used, eg if the actor was round. The `touchable` boolean parameter indicates if the actor's touchability should be respected. This enables hit detection for purposes other than touch on actors that are not touchable.

When `hit` is called on the stage, `hit` is called on the stage's root group, which in turn calls `hit` on each child. The first non-null actor found is returned as the actor deepest in the hierarchy that contains the given point.

## Event system ##

scene2d uses a generic event system. Each actor has a list of listeners that are notified for events on that actor. Events are propagated in two phases. First, during the "capture" phase an event is given to each actor from the root down to the target actor. Only capture listeners are notified during this phase. This gives parents a chance to intercept and potentially cancel events before children see them. Next, during the "normal" phase the event is given to each actor from the target up to the root. Only normal listeners are notified during this phase. This allows actors to handle an event themselves or let the parent have a try at it.

The event provided to each actor when it is notified contains state about the event. The target is the actor that the event originated on. The listener actor is the actor that the listener is attached to. The event also has a few important methods. If `stop` is called on the event, any remaining listeners for the current actor are still notified, but after those no other actors will receive the event. This can be used to prevent children (during the capture phase) or parents (during the normal phase) from seeing the event. If `cancel` is called on the event, it stops propagation the same as stop and also prevents any default action that would have been taken by the code that fired the event. E.g., if the event is for a check-box being checked, cancelling the event could prevent the check-box from being checked.

For example, imagine a group (a button) which has a child (a label). When the label is clicked, capture listeners are fired. Usually there are none. Next, the label's normal listeners are notified. The label is both the target and the listener actor. If the event was not stopped, the button gets the event and it's normal listeners are notified. The label is the target and the button is the listener actor. This continues up to the root. This system allows a single listener on a parent to handle events on its children.

### InputListener ###

EventListeners are added to actors to be notified about events. EventListener is an interface with a `handle(Event)` method. Implementations use instanceof to determine if they should handle the event. Typically, for each type of event a specific listener class is provided for convenience. Eg, InputEvent is fired when an actor receives an input event and InputListener is provided to simplify listening for InputEvent. Just override the InputListener methods for the input events you are interested in:

```java
actor.addListener(new InputListener() {
	public boolean touchDown (InputEvent event, float x, float y, int pointer, int button) {
		System.out.println("down");
		return true;
	}
	
	public void touchUp (InputEvent event, float x, float y, int pointer, int button) {
		System.out.println("up");
	}
});
```

Note InputListener has many other methods that can be overridden which are not shown above.

To handle touch and mouse events, override `touchDown`, `touchDragged`, and `touchUp`. The touchDragged and touchUp events will only be received if touchDown returns true. Also, the touchDragged and touchUp events will be received even if they do not occur over the actor. This simplifies the most common touch event use cases.

To handle enter and exit events when a touch and or the mouse enters or exits an actor, override `enter` or `exit`. 

To handle mouse movement when no mouse button is down (which only occurs on the desktop), override the `mouseMoved` method.

To handle mouse scrolling (which only occurs on the desktop), override the `scrolled` method. This will only be called on the actor with scroll focus, which is set and cleared by calling setScrollFocus on the stage.

To handle key input, override the `keyDown`, `keyUp`, and `keyTyped` methods. These will only be called on the actor with keyboard focus, which is set and cleared by calling `setKeyboardFocus` on the stage.

If `setTouchable(false)` or `setVisible(false)` is called on an actor, it will not receive input events.

### Other listeners ###

Other listeners are provided for common handling of input events. ClickListener has a boolean that is true when a touch or mouse button is pressed over the actor and a `clicked` method that is called when the actor is clicked. ActorGestureListener detects tap, longPress, fling, pan, zoom, and pinch gestures on an actor.

```java
actor.addListener(new ActorGestureListener() {
	public boolean longPress (Actor actor, float x, float y) {
		System.out.println("long press " + x + ", " + y);
		return true;
	}

	public void fling (InputEvent event, float velocityX, float velocityY, int button) {
		System.out.println("fling " + velocityX + ", " + velocityY);
	}

	public void zoom (InputEvent event, float initialDistance, float distance) {
		System.out.println("zoom " + initialDistance + ", " + distance);
	}
});
```

## Actions ##

Each actor has a list of actions. These are updated each frame by the Actor `act` method. Many types of actions are included with libgdx. These can be instantiated, configured, and added to an actor. When the action is complete, it will automatically be removed from the actor.

```java
MoveToAction action = new MoveToAction();
action.setPosition(x, y);
action.setDuration(duration);
actor.addAction(action);
```

### Action pooling ###

To avoid allocating a new action each time it is needed, a pool can be used:

```java
Pool<MoveToAction> pool = new Pool<MoveToAction>() {
	protected MoveToAction newObject () {
		return new MoveToAction();
	}
};
MoveToAction action = pool.obtain();
action.setPool(pool);
action.setPosition(x, y);
action.setDuration(duration);
actor.addAction(action);
```

When the action is complete, it is removed from the actor and put back in the pool for reuse. The above code is quite verbose though. The Actions class (note the plural) provides convenience methods. It can provide pooled actions:

```java
MoveToAction action = Actions.action(MoveToAction.class);
action.setPosition(x, y);
action.setDuration(duration);
actor.addAction(action);
```

Even better, the Actions class has methods that return a pooled and configured action.

```java
actor.addAction(Actions.moveTo(x, y, duration));
```

The Actions class has many of these convenience methods for completely configuring any of the built in actions using a single method call. This can be made even simpler by using a static import, which allows the static methods to be referenced on Actions without specifying "Actions." each time. Note that Eclipse will not add a static import for you, you must add it yourself.

```java
import static com.badlogic.gdx.scenes.scene2d.actions.Actions.*;
...
actor.addAction(moveTo(x, y, duration));
```

### Complex actions ###

More complex actions can be built by running actions at the same time or in sequence. ParallelAction has a list of actions and runs them at the same time. SequenceAction has a list of actions and runs them one after another. Use of the static import for the Actions class makes defining complex actions very easy:

```java
actor.addAction(sequence(moveTo(200, 100, 2), color(Color.RED, 6), delay(0.5f), rotateTo(180, 5)));
```

### Action completion ###

To run code when an action is complete, a sequence with a RunnableAction can be used:

###
actor.addAction(sequence(fadeIn(2), run(new Runnable() {
	public void run () {
		System.out.println("Action complete!");
	}
})));
###

### Interpolation ###

The tweening curve can be set for actions that manipulate an actor over time. This is done by giving the action an instance of Interpolation. The Interpolation class has many static fields for convenience, or you can write your own. See InterpolationTest for an interactive demo of each interpolation.

```java
MoveToAction action = Actions.action(MoveToAction.class);
action.setPosition(x, y);
action.setDuration(duration);
action.setInterpolation(Interpolation.bounceOut);
actor.addAction(action);
```

The Actions class has methods that can take an interpolation and a static import can be used to more easily access the Interpolation static fields. Here the `bounceOut` and `swing` interpolations are used:

```java
import static com.badlogic.gdx.scenes.scene2d.actions.Actions.*;
import static com.badlogic.gdx.math.Interpolation.*;
...
actor.addAction(parallel(moveTo(250, 250, 2, bounceOut), color(Color.RED, 6), delay(0.5f), rotateTo(180, 5, swing)));
actor.addAction(forever(sequence(scaleTo(2, 2, 0.5f), scaleTo(1, 1, 0.5f), delay(0.5f))));
```

### External Links ##

 * [netthreads](http://www.netthreads.co.uk/2012/01/31/libgdx-example-of-using-scene2d-actions-and-event-handling/) A fully documented scene2d example game.
 * [gdx-ui-app](https://github.com/broken-e/gdx-ui-app) A library on top of scene2d for easier development.
 * [Should I use scene2d for my game?](http://www.java-gaming.org/index.php/topic,29584.msg271857.html#msg271857)
 * [Street Race game tutorial](http://theinvader360.blogspot.co.uk/2013/05/street-race-swipe-libgdx-scene2d.html)