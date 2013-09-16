# Setting up Box2D with libgdx #

Box2D is a 2D physics library. It is one of the most popular physics libraries for 2D games and has been ported to many languages and many different engines, including libgdx.

The Box2D implementation in libgdx is a thin Java wrapper about the C++ engine. Therefore, the excellent [official Box2D manual (PDF)](http://box2d.org/manual.pdf) may also come in handy.

For more documentation related to Box2D, please visit [box2d.org](http://box2d.org/). Any Box2D questions not related to libgdx specifically can best be asked on their forum.

## Table of Contents ##

  * [Creating a World](#creating_a_world)
  * [Debug Renderer](#debug_renderer)
  * [Stepping the simulation](#stepping_the_simulation)
  * [Rendering](#rendering)
  * [Objects/Bodies](#objects_bodies)
    * [Dynamic Bodies](#dynamic_bodies)
    * [Static Bodies](#static_bodies)
    * [Kinematic Bodies](#kinematic_bodies)
  * [Impulses/Forces](#impulses_forces)
  * [Joints and Gears](#joints_and_gears)
  * [Fixture Shapes](#fixture_shapes)
  * [Sprites and Bodies](#sprites_and_bodies)
  * [Sensors](#sensors)
  * [Contact Listeners](#contact_listeners)
  * [Code Snippets](#code_snippets)
  * [Resources](#resources)




## <a id="creating_a_world"></a>Creating a World ##

When setting up Box2D the first thing we need is a world. The world object is basically what holds all your physics objects/bodies and simulates the reactions between them. It does not however render the objects for you; for that you will use libgdx graphics functions. That said, libgdx does come with a Box2D debug renderer which is extremely handy for debugging your physics simulations, or even for testing your game-play before writing any rendering code.

To create the world we use the following code:

```java
World world = new World(new Vector2(0, -10), true); 
```

The first argument we supply is a 2D vector containing the gravity: `0` to indicate no gravity in the horizontal direction, and -10 is a downwards force like in real life (assuming your y axis points upwards). These values can be anything you like, but remember to stick to a constant scale. In Box2D 1 unit = 1 metre, and you should scale your graphics accordingly for the most realistic results. 

Most people use static variables for converting between Box2D sizes and your game object sizes, for example:

```java
static final float WORLD_TO_BOX = 0.01f;
static final float BOX_TO_WORLD = 100f;
```

If you wanted to get the Box2D size for one of your game sprites, you would convert it by doing:

```java
sprite.getHeight() * WORLD_TO_BOX;
```

The second value in the world creation is a boolean value which tells the world if we want objects to sleep or not. Generally we want objects to sleep as this conserves CPU usage, but there are situations where you might not want your objects to sleep.



## <a id="debug_renderer"></a>Debug Renderer ##

The next thing we are going to do is setup our debug renderer. You generally will not use this in a released version of your game, but for testing purposes we will set it up now like so:

```java
Box2DDebugRenderer debugRenderer = new Box2DDebugRenderer();
```



## <a id="stepping_the_simulation"></a>Stepping the simulation ##

To update our simulation we need to tell our world to step. Stepping basically updates the world objects through time. The best place to call our step function is at the end of our `render()` loop. In a perfect world everyones frame rate is the same

```java
world.step(1/60f, 6, 2);
```

The first argument is the time-step, or the amount of time you want your world to simulate. In most cases you want this to be a fixed time step. libgdx recommends `1/45f` (which is 1/45th of a second) for mobile phones or `1/300f` (1/300th of a second). 

The other two arguments are `velocityIterations` and `positionIterations`. For now we will leave these at `6` and `2`, but you can read more about them in the Box2D documentation.



## <a id="rendering"></a>Rendering ##

It is recommended that you render all your graphics before you do your physics step, otherwise it will be out of sync. To do this with our debug renderer we do the following:

```java
debugRenderer.render(world, camera.combined);
```

The first argument is our Box2D world and the second argument is our libgdx camera.



## <a id="objects_bodies"></a>Objects/Bodies ##

Now if you run your game it will be pretty boring as nothing happens. The world steps but we don’t see anything as we don’t have anything to interact with it. So now we’re going to add some objects.

In Box2D our objects are called _bodies_, and each body is made up of one of more _fixtures_, which have a fixed position and orientation within the body. Our fixtures can be any shape you can imagine or you can combine a variety of different shaped fixtures to make the shape you want.

A fixture has a shape, density, friction and restitution attached to it. Shape is obvious. Density is the mass per square metre: a bowling ball is very dense, yet a balloon isn’t very dense at all as it is mainly filled with air. Friction is the amount of opposing force when the object rubs/slides along something: a block of ice would have a very low friction but a rubber ball would have a high friction. Restitution is how bouncy something is: a rock would have a very low restitution but a basketball would have a fairly high restitution. A body with a restitution of 0 will come to a halt as soon as it hits the ground, whereas a body with a restitution of 1 would bounce to the same height forever.

Bodies come in three different types: dynamic, kinematic and static. Each type is described below.


### <a id="dynamic_bodies"></a>Dynamic Bodies ###

Dynamic bodies are objects which move around and are affected by forces and other dynamic, kinematic and static objects. Dynamic bodies are suitable for any object which needs to move and be affected by forces. 

We have now learned about fixtures which make up our bodies, so let's get dirty and start to create some bodies and add fixtures to them!

```java
// First we create a body definition
BodyDef bodyDef = new BodyDef();
// We set our body to dynamic, for something like ground which doesn't move we would set it to StaticBody
bodyDef.type = BodyType.DynamicBody;
// Set our body's starting position in the world
bodyDef.position.set(100, 300);

// Create our body in the world using our body definition
Body body = world.createBody(bodyDef);

// Create a circle shape and set its radius to 6
CircleShape circle = new CircleShape();
circle.setRadius(6f);

// Create a fixture definition to apply our shape to
FixtureDef fixtureDef = new FixtureDef();
fixtureDef.shape = circle;
fixtureDef.density = 0.5f; 
fixtureDef.friction = 0.4f;
fixtureDef.restitution = 0.6f; // Make it bounce a little bit

// Create our fixture and attach it to the body
Fixture fixture = body.createFixture(fixtureDef);

// Remember to dispose of any shapes after you're done with them!
// BodyDef and FixtureDef don't need disposing, but shapes do.
circle.dispose();
```

Now we have create a ball like object and added it to our world. If you run the game now you should see a ball fall down the screen. This is still fairly boring though, as it has nothing to interact with. So let's create a floor for our ball to bounce on.



### <a id="static_bodies"></a>Static Bodies ###

Static bodies are objects which do not move and are not affected by forces. Dynamic bodies are affected by static bodies. Static bodies are perfect for ground, walls, and any object which does not need to move. Static bodies require less computing power.

Let's go ahead and create our floor as a static body. This is much like creating our dynamic body earlier.

```java
// Create our body definition
BodyDef groundBodyDef =new BodyDef();  
// Set its world position
groundBodyDef.position.set(new Vector2(0, 10));  

// Create a body from the defintion and add it to the world
Body groundBody = world.createBody(groundBodyDef);  

// Create a polygon shape
PolygonShape groundBox = new PolygonShape();  
// Set the polygon shape as a box which is twice the size of our view port and 20 high
// (setAsBox takes half-width and half-height as arguments)
groundBox.setAsBox(camera.viewportWidth, 10.0f);
// Create a fixture from our polygon shape and add it to our ground body  
groundBody.createFixture(groundBox, 0.0f); 
// Clean up after ourselves
groundBox.dispose();
```

See how we created a fixture without the need to define a `FixtureDef`? If all you need to specify is a shape and a density, the `createFixture` method has a useful overload for that.

Now if you run the game you should see a ball fall and then bounce on our newly created ground. Play around with some of the different values for the ball like density and restitution and see what happens.



### <a id="kinematic_bodies"></a>Kinematic Bodies ###

Kinematic bodies are somewhat in between static and dynamic bodies. Like static bodies, they do not react to forces, but like dynamic bodies, they do have the ability to move. Kinematic bodies are great for things where you, the programmer, want to be in full control of a body's motion, such as a moving platform in a platform game.

It is possible to set the position on a kinematic body directly, but it's usually better to set a velocity instead, and letting Box2D take care of position updates.

You can create a kinematic body in much the same way as the dynamic and static bodies above. Once created, you can control the velocity like this:

```java
// Move upwards at a rate of 1 meter per second
kinematicBody.setLinearVelocity(0.0f, 1.0f);
```

## <a id="impulses_forces"></a>Impulses/Forces ##

Impulses and Forces are used to move a body in addition to gravity and collision. 

Forces occur gradually over time to change the velocity of a body. For example, a rocket lifting off would slowly have forces applied as the rocket slowly begins to accelerate.

Impulses on the other hand make immediate changes to the body's velocity. For example, playing Pac-Man the character always moved at a constant speed and achieved instant velocity upon being moved.

First you will need a Dynamic Body to apply forces/impulses to, see the [Dynamic Bodies](#dynamic_bodies) section above.

**Applying Force**

Forces are applied in Newtons at a World Point. If the force is not applied to the center of mass, it will generate torque and affect the angular velocity.

```java
// Apply a force of 1 meter per second on the X-axis at pos.x/pos.y of the body slowly moving it right
dynamicBody.applyForce(1.0f, 0.0f, pos.x, pos.y, true);

// If we always want to apply force at the center of the body, use the following
dynamicBody.applyForceToCenter(1.0f, 0.0f, true);
```

**Applying Impulse**

Impulses are just like Forces with the exception that they immediately modify the velocity of a body. As such if the impulse is not applied at the center of a body, it will create torque which modifies angular velocity. Impulses are applied in Newton-seconds or kg-m/s.

```java
// Immediately set the X-velocity to 1 meter per second causing the body to move right quickly
dynamicBody.applyLinearImpulse(1.0f, 0, pos.x, pos.y, true);
```

Keep in mind applying forces or impulses will wake the body. Sometimes this behavior is undesired. For example, you may be applying a steady force and want to allow the body to sleep to improve performance. In this case you can set the wake boolean value to false.

```java
// Apply impulse but don't wake the body
dynamicBody.applyLinearImpulse(0.8f, 0, pos.x, pos.y, false);
```

**Player Movement Example**

In this example, we will make a player run left or right and accelerate to a maximum velocity, just like Sonic the Hedgehog. For this example we have already created a Dynamic Body named 'player'. In addition we have defined a MAX_VELOCITY variable so our player won't accelerate beyond this value. Now it's just a matter of applying a linear impulse when a key is pressed.

```java
Vector2 vel = this.player.body.getLinearVelocity();
Vector2 pos = this.player.body.getPosition();
		
// apply left impulse, but only if max velocity is not reached yet
if (Gdx.input.isKeyPressed(Keys.A) && vel.x > -MAX_VELOCITY) {			
     this.player.body.applyLinearImpulse(-0.80f, 0, pos.x, pos.y, true);
}

// apply right impulse, but only if max velocity is not reached yet
if (Gdx.input.isKeyPressed(Keys.D) && vel.x < MAX_VELOCITY) {
     this.player.body.applyLinearImpulse(0.80f, 0, pos.x, pos.y, true);
}
```

## <a id="joints_and_gears"></a>Joints and Gears ##

Coming soon

## <a id="fixture_shapes"></a>Fixture Shapes ##

Coming soon

## <a id="sprites_and_bodies"></a>Sprites and Bodies ##

The easiest way to manage a link between your sprites or game objects and Box2D is with Box2D’s User Data. You can set the user data to your game object and then update the object's position based on the Box2D body.

Setting a bodies user data is easy

```java
body.setUserData(Object);
```

This can be set to any Java object. It is also good to create your own game actor/object class which allows you to set a reference to its physics body.

Fixtures can also have user data set to them in the same way.

To update all your actors/sprites you can loop through all the world's bodies easily in your game/render loop.

```java
Iterator<Body> bi = world.getBodies();
        	   
while (bi.hasNext()){
    Body b = bi.next();

    // Get the bodies user data - in this example, our user 
    // data is an instance of the Entity class
    Entity e = (Entity) b.getUserData();

    if (e != null) {
        // Update the entities/sprites position and angle
        e.setPosition(b.getPosition().x, b.getPosition().y);
        // We need to convert our angle from radians to degrees
        e.setRotation(MathUtils.radiansToDegrees * b.getAngle());
    }
}
```

Then render your sprites using a libgdx `SpriteBatch` as usual.

## <a id="sensors"></a>Sensors ##
Sensors are Bodies that do not produce automatic response when collide. This is interesting when is needed to be in complete control of what happens when two shapes overlap.
To imagine that, we may think of a drone that has some kind of circular distance of sight. This body should follow the drone but shouldn't make physical reactions to it, and the other bodies. It should detect when some target is inside it's shape.
Every body can be a Sensor, it just must have the 'isSensor' flag set to true. An example of this would be like this:

```java
//At the definition of the Fixture
fixtureDef.isSensor = true;
```

In order to listen to this sensor contact, we need to implement the ContactListener interface methods.

## <a id="contact_listeners"></a>Contact Listeners ##
The Contact Listeners listen to a fixture who is colliding. This methods mainly contain a Contact object. The contact object contain information about the two bodies involved.
The beginContact method is called when the object overlap the other. When the object moves out of this collision the endContact method is called.

```java
public class ListenerClass implements ContactListener {
			
      @Override
			public void endContact(Contact contact) {
				
			}
			
			@Override
			public void beginContact(Contact contact) {
				
			}
		};
```

We might get information about the bodies from the contact fixtures.
Depending of the application desing, the Entity class should be referenced in the Body or Fixture user data, so we can use it from the contact and make some changes (e.g. change the player health).

## <a id="code_snippets"></a>Code Snippets ##

Coming soon

## <a id="resources"></a>Resources ##

There are a lot of really good Box2D resources out there and most of the code can be easily converted to libgdx.

  * http://box2d.org Documentation and forums are a great place to find help.
  * http://www.iforce2d.net/b2dtut/ A really good tutorial series on Box2D. Covers a lot of different problems which you will more than likely run across in your game development

## <a id="tools"></a>Tools ##

The following is a list of tools for use with box2d and libgdx:

   * <a href="https://www.iforce2d.net/rube">RUBE</a> editor for creating box2d worlds.  Use <a href="https://github.com/tescott/RubeLoader">RubeLoader</a> for loading RUBE data into libgdx.
   * Obli's <a href="http://www.aurelienribon.com/blog/projects/physics-body-editor/">Physics Body Editor</a>