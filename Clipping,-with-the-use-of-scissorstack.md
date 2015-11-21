# Clipping #

```java
Rectangle scissors = new Rectangle();
Rectangle clipBounds = new Rectangle(x,y,w,h);
ScissorStack.calculateScissors(camera, spriteBatch.getTransformMatrix(), clipBounds, scissors);
ScissorStack.pushScissors(scissors);
spriteBatch.draw(...);
spriteBatch.flush();
ScissorStack.popScissors();
```

This will limit rendering to within the bounds of the rectangle "clipBounds". You may also need to flush or end the `spriteBatch` before starting the active scissor region (that is, before calling `ScissorStack.pushScissors`) to prevent queued draw calls from before the scissor start getting flushed inside the active scissor region.

It is also possible push multiple rectangles. Only the pixels of the sprites that are within <b>all</b> of the rectangles will be rendered.