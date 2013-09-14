# fixme: ToC

# About the Bullet Physics extension #
Bullet is a 3D Collision Detection and Rigid Body Dynamics Library. The Library is Open Source and free for commercial use, under the zlib license ([more info](http://bulletphysics.org/mediawiki-1.5.8/index.php/LICENSE)).

The Bullet physics extension is a Java wrapper for the C++ engine. This page provides information about using that wrapper. For more documentation related to the Bullet engine, please visit [bulletphysics.org](http://bulletphysics.org). Any Bullet questions not related to libgdx specifically can best be asked on [their forum](http://www.bulletphysics.org/Bullet/phpBB3/).

Practical information and examples on how to use the bullet wrapper can also be found at the [Bullet tests](https://github.com/libgdx/libgdx/tree/master/tests/gdx-tests/src/com/badlogic/gdx/tests/bullet).

# Using Bullet in your project #

## Setting up Bullet with libgdx ##
To use bullet physics in your project, you’ll need to add gdx-bullet.jar to your main project. Alternatively you can add the gdx-bullet project to the projects of the build path of your main project.

For your desktop project you’ll need to add the gdx-bullet-natives.jar to the libraries.

For your android project you’ll need to copy the armeabi/libgdx-bullet.so and armeabi-v7a/libgdx-bullet.so files to the libs folder in your android project.

Bullet isn’t supported for GWT at the moment.

## Initializing Bullet ##

Before you can use Bullet, you’ll need to load the libraries. This can be done by adding the following line in your create method:
```java
Bullet.init();
```

Be aware not to use bullet before it is initialized. For example, the following will result in an error because the `btGhostPairCallback` is created before the library is loaded.
```java
public class InvokeRuntimeExceptionTest {
  final static btGhostPairCallback ghostPairCallback = new btGhostPairCallback();
}
```

## Working with Bullet wrapper ##
The wrapper tends to follow the original bullet class names. Meaning that most classes are prefixed with “bt”. There are a few exceptions on this, which are mostly nested structs. These are custom implemented directly into the `com.badlogic.gdx.physics.bullet` package. Unfortunately some nested structs and some base classes are not suitable for a one on one translation. See the custom classes section for more information on that. If you find a class that is missing you can post it on the forums so it can be added to the wrapper.

## Callbacks ##

Callbacks require some special attention. By default the wrapper only supports a one way interaction (from Java to C++). Callback interfaces, where C++ needs to call Java code are custom implemented. If you find a callback interface that isn't implemented yet, you can post it on the forums so it can be added to the wrapper.

List of callback interfaces (might not be complete):
 * `LocalShapeInfo`
 * `LocalRayResult`
 * `RayResultCallback`
 * `ClosestRayResultCallback`
 * `AllHitsRayResultCallback`
 * `LocalConvexResult`
 * `ConvexResultCallback`
 * `ClosestConvexResultCallback`
 * `ContactResultCallback`
 * `btMotionState`
 * `btIDebugDraw`
 * `InternalTickCallback`
 * `ContactListener`
 * `ContactCache`

## Properties ##
Properties are encapsulated by getter and setter methods. The naming of the getter and setter methods omits the "m`_`" prefix. For example, the `m_collisionObject` member of the native class `btCollisionObjectWrapper` is implemented as `getCollisionObject()` and `setCollisionObject(...)`.

## Creating and destroying objects ##
Every time you create a bullet class in Java it also creates the corresponding class in C++. While the Java object is maintained by the garbage collector, the C++ object isn’t. To avoid having orphaned C++ objects resulting in memory leaks, the C++ object is by default automatically destroyed when the Java object is destroyed by the garbage collector. 

While this might be useful in some cases, it’s merely a fail-safe and you shouldn't rely on it. Since you can’t control the garbage collector, you can’t control _if_, _when_ and _in which order_ the objects are actually being destroyed. Therefor the wrapper logs an error when an object is automatically destroyed by the garbage collector. You can disable logging using the second argument of the `Bullet.init()` method.

Instead you should keep a reference to every object you create until it’s not needed anymore and then destroy it yourself. You can destroy the C++ object by calling the `.dispose()` method on the Java object, after which you should remove all references to the Java object since it’s unusable after that. 

The above is only true for the objects you are responsible of. Which are all Bullet classes you create with the new keyword and also includes classes you create using helper methods. You don’t have to dispose objects that are returned by regular methods or provided to you in callback methods.

## Referencing objects ##
A stated above, you should keep a reference to every Bullet class and call the dispose method when it’s no longer needed. When your application becomes more complex and objects are shared amongst multiple other objects, it can become difficult to keep track of references. Therefor the bullet wrapper support reference counting.

Reference counting is disabled by default. To disable it, call `Bullet.init();` with the first argument set to true:
```java
Bullet.init(true);
```

When using reference counting, you must call the `obtain()` method on each object you need to reference. When you no longer need to reference an object, you must call the `release()` method. The release method will dispose the object if it’s doesn't have any more references to it.

Some wrapper classes help you in managing references. For example the `btCompoundShape` class obtains a reference to all its child shapes and releases them when it is disposed.

## Extending classes ##
You can extend the bullet classes, but it’s recommended not to do so except for callback classes (in which case you should only override the intended methods). The information you add to a class is not available in C++. Furthermore the result of any method of the bullet wrapper that returns a class you’ve overridden will not implement that class. For example:
``java
btCollisionShape shape = collisionObjectA.getCollisionShape();
```

This will create a new Java btCollisionShape class which doesn’t implement any extended class.

There is one exception on this for btCollisionObject, where the wrapper tries to reuse the same Java class. Furthermore the Java implementation of the btCollisionObject class adds a `userData` member which can be used to attach additional data to the object. To accomplish this the wrapper maintains an array with references to all btCollisionObject instances. You can access that array using the static field `btCollisionObject.instances`. Check the [#btCollisionObject btCollisionObject] section for detailed information on this.

Some classes provide a static `upcast` method which can be used to cast the object to an higher class. For example:

```java
btRigidBody bodyA = btRigidBody.upcast(collisionObjectA);
```

## Comparing classes ##
You can compare wrapper classes using the `equals()` method, which checks if the classes both wrap the same native class. To get the pointer to the underlying C++ class you can use the `getCPointer` method of the specific object. You can also compare these pointers to check if Java classes wrap the same C++ class.

## Common classes ##
Bullet uses some classes also available in the libgdx core. While these bullet classes are available for you to use, the wrapper tries to use the libgdx class where possible. Currently this is implemented for:

| *Bullet* | *Libgdx* |
|:--------:|:--------:|
| btVector3 | Vector3 |
| btQuaternion | Quaternion |
| btMatrix3x3 | Matrix3 |
| btTransform | Matrix4 |

<sub>Note that the conversion from Matrix4 to btTransform might lose some information, because btTransform only contains an origin and rotation.</sub>

To avoid creating objects for these common classes, the wrapper reuses the same instances. Therefore, be aware of the following two cases:

 1. The result of wrapper methods that return such a class are overwritten by the next method that returns the same type:
```java
// Wrong method:
Matrix4 transformA = collisionObjectA.getWorldTransform();
// transformA now holds the worldTransform of collisionObjectA
Matrix4 transformB = collisionObjectB.getWorldTransform();
// transformA and transformB are the same object and now holds the worldTransform of collsionObjectB

// Correct method:
transformA.set(collisionObjectA.getWorldTransform());
transformB.set(collisionObjectB.getWorldTransform());
```
 2. The arguments of interface callbacks with arguments of such a class are unusable after the call:
```java
// Wrong method:
@Override
public void setWorldTransform (final Matrix4 worldTrans) {
	transform = worldTrans;
}
// Correct method:
@Override
public void setWorldTransform (final Matrix4 worldTrans) {
	transform.set(worldTrans);
}
```

## Using arrays ##
Where possible the wrapper uses direct ByteBuffer objects to pass arrays from Java to C++. This avoids copying the array on the call and allows you to share the same byte buffer for both OpenGL ES and Bullet. If needed you can create a new ByteByffer using `BufferUtils.newUnsafeByteBuffer`, which you should manually delete using `BufferUtils.disposeUnsafeByteBuffer`.

In cases where ByteBuffer can't be used or is unwanted to be used, a normal array is used. By default this means that the array is copied using iteration from Java to C++ at start of the method and copied back at the end of the method. To avoid this overhead the wrapper tries to use the Java array directly from within C++ where possible using critical arrays. During such method Java garbage collecting is blocked. An example of such method is `btBroadphasePairArray.getCollisionObjects`.

## Contact Callbacks ##
Contact callbacks allow you to be notified when a contact/collision on two objects occur ([more info](http://bulletphysics.org/mediawiki-1.5.8/index.php/Collision_Callbacks_and_Triggers#Contact_Callbacks)). By default there are three callbacks: [`onContactAdded`](http://bulletphysics.org/mediawiki-1.5.8/index.php/Collision_Callbacks_and_Triggers#gContactAddedCallback), [`onContactProcessed`](http://bulletphysics.org/mediawiki-1.5.8/index.php/Collision_Callbacks_and_Triggers#gContactProcessedCallback) and [`onContactDestroyed`](http://bulletphysics.org/mediawiki-1.5.8/index.php/Collision_Callbacks_and_Triggers#gContactDestroyedCallback)) . The wrapper adds two additional callbacks: `onContactStarted` and `onContactEnded`. The callbacks are global (independent of e.g. the collision world), there can be only one implementation per callback active at the same time.

### Contact Listeners ###
You can extend the ContactListener class to implement one or more callbacks:
```java
public class MyContactListener extends ContactListener {
	@Override
	public void onContactStarted (btCollisionObject colObj0, btCollisionObject colObj1) {
		// implementation
	}
	@Override
	public void onContactProcessed (int userValue0, int userValue1) {
		// implementation
	}
}
```

Note that there can only be one listener enabled for each callback at a time. You can use the `enable();` method to set the listener active, which disables any other listeners on that particular callback. Use the `disable();` method to stop being notified for that particular callback. Instantiating a listener automatically enables that callback and destroying (`dispose();` method) automatically disables it.

The ContactListener class provides one or more method signatures per callback you can override. For example the `onContactAdded` callback can be overridden using the following signatures:

```java
boolean onContactAdded(btManifoldPoint cp, btCollisionObjectWrapper colObj0Wrap, int partId0, int index0, btCollisionObjectWrapper colObj1Wrap, int partId1, int index1);

boolean onContactAdded(btManifoldPoint cp, btCollisionObject colObj0, int partId0, int index0, btCollisionObject colObj1, int partId1, int index1);

boolean onContactAdded(btManifoldPoint cp, int userValue0, int partId0, int index0, int userValue1, int partId1, int index1);

boolean onContactAdded(btCollisionObjectWrapper colObj0Wrap, int partId0, int index0, btCollisionObjectWrapper colObj1Wrap, int partId1, int index1);

boolean onContactAdded(btCollisionObject colObj0, int partId0, int index0, btCollisionObject colObj1, int partId1, int index1);

boolean onContactAdded(int userValue0, int partId0, int index0, int userValue1, int partId1, int index1);
```

As you can see it has three methods which provide the `btManifoldPoint` and three which don’t.  To provide the actual collision objects, you can choose between either the `btCollisionObjectWrapper`, `btCollisionObject` or the `userValue`.

Make sure to override the method that only provides the arguments you are actually going to use. For example, if you are not going to use the `btManifoldPoint` then it wouldn’t make sense to create an object for that argument each time the callback is called. Likewise using `btCollisionObject` is more performant than using `btCollisionObjectWrapper`, because the `btCollisionObject` is reused. The `userValue` is even more performant, because the object isn’t mapped at all  (see [#btCollisionObject btCollisionObject] on how to use the useValue).

To identify a contact along the added, processed and destroyed callbacks, you can use the `setUserValue(int);` and `getUserValue();` of the `btManifoldPoint` instance that the callback provides. This is also the value supplied to the `onContactDestroyed(int)` method of the `ContactDestroyedListener` class.

### Contact Filtering ###

Contact callbacks are invoked a lot. JNI bridging between C++ and Java on every call adds quite an overhead, which decreases performance. Therefor the bullet wrapper allows you to specify for which objects you would like to receive contacts. This is done by contact filtering.

Similar to collision filtering, for every `btCollisionObject`, you can specify a flag using the `setContactCallbackFlag(int);` method and a filter using the `setContactCallbackFilter(int);` method. The filter of object A matches object B if `A.filter & B.flag == B.flag`. Only if one or both of the filters match the contact is passed to the listener.

```java
static int PLAYER_FLAG = 2; // second bit
static int COIN_FLAG = 4; // third bit
btCollisionObject player;
btCollisionObject coin;
...
player.setCollisionFlag(PLAYER_FLAG);
coin.setCollisionFilter(PLAYER_FLAG);
// The listener will only be called if a coin collides with player
```

By default the `contactCallbackFlag` of a `btCollisionObject` is set to 1 and the `contactCallbackFilter` is set to 0. Note that setting the flag to zero will cause the callback always to be invoked for that object (because `x & 0 == 0`).

Whether or not contact filtering is used, is decided by which method signature you override. For every callback that supports contact filtering the `ContactListener` class provides method signatures with the `boolean match0` and `boolean match1` arguments. If you override such method, contact filtering is used on that method. If you override a method that doesn’t have the `boolean match` arguments, then contact filtering is not used for that method.

You can use the `boolean match0` and `boolean match1` values to check which of both filters matches.
```java
public class MyContactListener extends ContactListener {
	@Override
	public void onContactEnded (int userValue0, boolean match0, int userValue1, boolean match1) {
		if (match0) {
			// collision object 0 (userValue0) matches
		}
		if (match1) {
			// collision object 1 (userValue1) matches
		}
	}
}
```

Even when using contact filtering, the callbacks can be invoked quite often on collision. To avoid this you can set the filter to zero after processing. For example, in the case of the player and the coin, it's best to let the coin collide with the player and than set it's filter to zero on first contact, instead of letting the player collide with the coin.

## Custom classes ##
In some cases it's not possible to wrap a C++ bullet class/method in a Java class/method, in which case a custom class or method is used to bridge the two. The following list describes those. Note that the list might not be complete.

### btCollisionObject ###

The btCollisionObject is modified to reuse Java objects instead of creating a new Java object every time. This is done using the static `btCollisionObject.instances` map. To remove an object from the map and delete the native object use the `dispose` method.

Besides reusing instances, the Bullet wrappers allows you to provide an unique number to identify the instance. For example the index/ID of the entity in your entity system. Some frequently called methods allow you to use that value instead of the instance itself. This completely eliminates the overhead of mapping C++ and Java instances. You can set this value using the `setUserValue(int);` method and retrieve the value using the `getUserValue();` method.

```java
public class MyGameObject {
  public btCollisionObject body;
}
...
Array<MyGameObject> gameObjects;
...
gameObjects.add(myGameObject);
myGameObject.setUserValue(gameObjects.size-1);
```

You can use the `userData` field to add some additional data. For example:
```java
btCollisionObject obj = new btCollisionObject();
obj.userData = myGameObject;
...
if (obj.userData instanceof MyGameObject)
  myGameObject = (MyGameObject)obj.userData;
```

`btCollisionObject` adds the methods `takeOwnership` and `releaseOwnership` which can be used to remove or make the wrapper responsable for destroying the native object when the Java object is destroyed by the garbage collector.

The `btCollisionObject` also adds the following methods:
 * `getAnisotropicFriction(Vector3)`
 * `getWorldTransform(Matrix4)`
 * `getInterpolationWorldTransform(Matrix4)`
 * `getInterpolationLinearVelocity(Vector3)`
 * `getInterpolationAngularVelocity(Vector3)`
 * `getContactCallbackFlag()` and `setContactCallbackFlag(int)`
 * `getContactCallbackFilter()` and `setContactCallbackFilter(int)`

### ClosestNotMeConvexResultCallback ###
The `ClosestNotMeConvexResultCallback` class is a custom `ClosestConvexResultCallback` implementation which you can use to perform a `convexSweepTest` on all objects except the specified one.

### ClosestNotMeRayResultCallback ###

The `ClosestNotMeRayResultCallback` class is a custom `ClosestRayResultCallback` implementation which you can use to perform a `rayTest` on all objects except the specified one.

### InternalTickCallback ###

The `InternalTickCallback` is implemented to bridge the callback required by `btDynamicsWorld#setInternalTickCallback` to a java class. You can extend the class and override `onInternalTick` method. You can use the `attach` and `detach` methods to start and stop getting tick callbacks.

### btDefaultMotionState ###

In some cases it's easier to use `btDefaultMotionState` instead of extending `btMotionState`. The following custom methods are available for `btDefaultMotionState`.
 * `getGraphicsWorldTrans(Matrix4)`
 * `getCenterOfMassOffset(Matrix4)`
 * `getStartWorldTrans(Matrix4)`
Note that extending `btMotionState` with your own implementation is the preferred method.

### btCompoundShape ###

The `btCompoundShape` class allows to keep a reference to child shapes, so you don't have to do that. To use it, use the `addChildShape` with the third `managed` argument set to true. Note that this will delete the managed child shape if the compound shape is deleted. Therefor the managed shapes should be exclusive for the compound shape.

### btIndexedMesh ###

The `btIndexedMesh` class adds the constructor:
 * `btIndexedMesh(Mesh)`
And the methods:
 * `setTriangleIndexBase(ShortBuffer)`
 * `setVertexBase(FloatBuffer)`
 * `set(Mesh)`
For easy constructing or setting a `btIndexedMesh` based on a `Mesh` instance or a vertex and index buffer. The buffers itself are not managed by the wrapper and should out-live the object.

### btTriangleIndexVertexArray ###

The `btTriangleIndexVertexArray` class adds the ability to maintain a reference to the Java `btIndexedMesh` classes it holds. To use it call `addIndexedMesh` with the last argument `managed` set to true. When the `btTriangleIndexVertexArray` is destroyed it will also destroy it's managed `btIndexedMesh` children.

Also, the `btTriangleIndexVertexArray` class adds the `addMesh` and `addModel` methods and likewise constructors, for easy constructing and setting the class.

### btBvhTriangleMeshShape ###

The `btBvhTriangleMeshShape` class adds the ability to maintain a reference to the Java `btStridingMeshInterface` class. To use it construct the class with the argument `managed` set to true. When the `btBvhTriangleMeshShape` is destroyed it will also destroy the managed `btStridingMeshInterface`.

Also, the `btBvhTriangleMeshShape` class add constructors for easy constructing one or more `Mesh` or `Model` instances.

### btConvexHullShape ###

The `btConvexHullShape` class adds a convenience constructor `btConvexHullShape(btShapeHull)`.

### btBroadphasePairArray ###

The `btBroadphasePairArray` class adds methods to get all collision objects within it at once:
```java
btBroadphasePairArray.getCollisionObjects(Array<btCollisionObject> out, btCollisionObject other, int[] tempArray)
btBroadphasePairArray.getCollisionObjectsValue(int[] out, btCollisionObject other)
```