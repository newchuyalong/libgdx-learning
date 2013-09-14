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

This will limit rendering to within the bounds of the rectangle "clipBounds".

It is also possible push multiple rectangles. Only the pixels of the sprites that are within <b>all</b> of the rectangles will be rendered.