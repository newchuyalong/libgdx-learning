# ATTENTION: Not merged yet (see [PR2202](https://github.com/libgdx/libgdx/pull/2202))

- [Introduction](#introduction)
- [Characters as Points](#characters-as-points)
- [What the hell is 2.5D?](#what-the-hell-is-25d)
- [Independent Facing](#independent-facing)
- [The Jargon](#the-jargon)
- [The Steering System API](#the-steering-system-api)
- [Individual Behaviors](#individual-behaviors)
- [Group Behaviors](#group-behaviors)
- [Combining Steering Behaviors](#combining-steering-behaviors)


## Introduction ##

In the early 1990s, computer scientist [Craig Reynolds](http://www.red3d.com/cwr) developed algorithmic steering behaviors for autonomous agents.
These behaviors allowed individual elements to navigate their digital environments with strategies for seeking, fleeing, wandering, arriving, pursuing, evading, avoiding an obstacle, following a path, and so on.
Actually these behaviors are fairly simple to understand and implement, but building a system of multiple characters that steer themselves according to simple, locally based rules, surprising levels of complexity emerge.
The most famous example is Reynolds's "boids" model for flocking behavior.

Notice that each steering behavior isn't trying to do everything. There is no behavior to avoid
obstacles while chasing a character and making detours via nearby power-ups. Each algorithm
does a single thing and only takes the input needed to do that. To get more complicated behaviors,
we will use algorithms to combine the steering behaviors and make them work together.

Steering behaviors produce steering forces that describe where and how an agent should move.
Although steering behaviors are fairly simple to implement, they can bring new problems to deal with.
Some behaviors may involve heavy manual tweaking, while others have to be carefully coded to avoid using large portions of CPU time.
When combining behaviors, care usually must be taken to avoid the possibility that two or more of them may cancel
each other out. There are means and ways around most of these problems though (except for the tweaking), and most often the benefits of steering behaviors far outweigh any disadvantages.

**Because manual tweaking is so ineluctable, it's recommended that you experiment with the [SteeringBehaviorTest](https://github.com/libgdx/libgdx/blob/master/tests/gdx-tests/src/com/badlogic/gdx/tests/SteeringBehaviorTest.java)
to get familiar with steering behaviors and their parameters. Especially, play with parameter values and see
what happens. Such experimentation really speaks more than 1000 words.**

## Characters as Points ##

Although a character consists of a 2D sprite or a 3D model that occupies some space in the game world, many movement algorithms assume that the character can be treated as
a single point. Collision detection, obstacle avoidance, and some other algorithms use the size of the character to influence their results, but movement itself assumes
the character is at a single point.

This is a process similar to that used by physics programmers who treat objects in the game as a "rigid body" located at its center of mass.
Collision detection and other forces can be applied to anywhere on the object, but the algorithm that determines the movement of the object converts
them so it can deal only with the center of mass.

## What the hell is 2.5D? ##

The steering behavior framework provides a **common interface for 2D and 2.5D**, which are suitable for most games.
Especially, 2.5D is an interesting hybrid of 2D and 3D geometry. In 2.5D you deal with a full 3D position but represent orientation as a single value, as if you were in 2D.

This is more than acceptable if you consider that many 3D games involve gravity. Most of the time a character is in contact with the ground, actually operating in two dimensions. Although jumping and using elevators involve movement through the 3rd dimension, characters usually remain upright. So the only component of their orientation we have to worry about is the rotation about the up direction. 

The simplified math of 2.5D is worth the decreased flexibility in most 3D games. Of course, for space shooters and flight simulators all the orientations are relevant to the AI, but in the vast majority of cases, 2.5D is an
optimal solution.

However, most steering behaviors work for full 3D too, excluded those behaviors that explicitly have an angular component, such as `ReachOrientation`, `Face`, `LookWhereYouAreGoing` and `Wander`. The other behaviors only work linearly, so none of them requires any modification for full 3D because the equations work unaltered.

## Independent Facing ##

The entire steering framework assumes that the direction a character is facing does not have to be its direction of motion, thus supporting **independent facing** that, among other things, allows you to smooth rotation over many frames when the direction abruptly changes.
Many games don't need independent facing though. In these cases the orientation should be updated "manually" so that it matches the direction of motion.
You can easily calculate the updated orientation with something along those lines: 
````java
	public static float calculateOrientationFromLinearVelocity (Steerable<T> character) {
		// If we haven't got any velocity, then we can do nothing.
		if (character.getLinearVelocity().isZero(MathUtils.FLOAT_ROUNDING_ERROR))
			return character.getOrientation();

		return character.vectorToAngle(character.getLinearVelocity());
	}
````
`Steerable<T>`? What's that? Don't worry for now, it will become clear very shortly. Just keep on reading. :)

## The Jargon ##

Don't be afraid, this is likely the shortest paragraph of this wiki page.
I just want to point out that in the next sections I will refer to a game object affected by steering behaviors with the following terms interchangeably: agent, owner, character, non-player character (NPC in short) and steerable.

## The Steering System API ##

The API is general enough to be integrated with the specific "engine" the developer is using, such as Scene2d, Box2d, Bullet, his own engine or anything else (hopefully).
The SteeringBehaviorTest mentioned above contains a sample integration for scene2d.

Basically, the developer will have to implement the following interfaces:
- [Steerable](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Steerable.html) gives access to the character's information required by steering behaviors.
- [Proximity](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Proximity.html) defines an area that is used by group behaviors to find and process the owner's neighbors.

There are two other classes that are heavily used by the steering system:
- [SteeringAcceleration](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/SteeringAcceleration.html) is a movement requested by the steering system. It is made up of two components, linear and angular acceleration.
- [SteeringBehavior](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/SteeringBehavior.html) calculates the linear and/or angular accelerations to be applied to its owner.

In short, each SteeringBehavior takes as input a Steerable and some behavior-specific parameters. When the [steer](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/SteeringBehavior.html#steer) method
of the SteeringBehavior is invoked a SteeringAcceleration is returned. Notice that only enabled behaviors can return a non-zero acceleration.

It is important to understand that the acceleration just produced is simply a movement request.
To make the character move you have to apply the accelerations either by using the methods provided by the underlying physics engine or by executing the correct formulas if you're using scene2d or any other non-physics engine.

For example, assuming you aren't using any particular engine, you can code a SteeringAgent implementing Steerable along those lines:
````java
// A simple steering agent for 2D.
// Of course, for 3D (well, actually for 2.5D) you have to replace all occurrences of Vector2 with  Vector3.
public class SteeringAgent implements Steerable<Vector2> {

	private static final SteeringAcceleration<Vector2> steeringOutput = 
		new SteeringAcceleration<Vector2>(new Vector2());

	Vector2 position;
	float orientation;
	Vector2 linearVelocity;
	float angularVelocity;
	float maxSpeed;
	boolean independentFacing;
	SteeringBehavior<Vector2> steeringBehavior;

	/* Here you should implement missing methods inherited from Steerable */

	// Actual implementation depends on your coordinate system.
	// Here we assume the y-axis is pointing upwards.
	@Override
	public float vectorToAngle (Vector2 vector) {
		return (float)Math.atan2(-vector.x, vector.y);
	}

	// Actual implementation depends on your coordinate system.
	// Here we assume the y-axis is pointing upwards.
	@Override
	public Vector2 angleToVector (Vector2 outVector, float angle) {
		outVector.x = -(float)Math.sin(angle);
		outVector.y = (float)Math.cos(angle);
		return outVector;
	}

	public void update (float delta) {
		if (steeringBehavior != null) {
			// Calculate steering acceleration
			steeringBehavior.steer(steeringOutput);

			// Apply steering acceleration to move this agent
			applySteering(steeringOutput, delta);
		}
	}

	private void applySteering (SteeringAcceleration<Vector2> steering, float time) {
		// Update position and linear velocity. Velocity is trimmed to maximum speed
		this.position.mulAdd(linearVelocity, time);
		this.linearVelocity.mulAdd(steering.linear, time).limit(this.getMaxLinearSpeed());

		// Update orientation and angular velocity
		if (independentFacing) {
			this.orientation += angularVelocity * time;
			this.angularVelocity += steering.angular * time;
		} else {
			// For non-independent facing we have to align orientation to linear velocity
			float newOrientation = calculateOrientationFromLinearVelocity(this);
			if (newOrientation != this.orientation) {
				this.angularVelocity = (newOrientation - this.orientation) * time;
				this.orientation = newOrientation;
			}
		}
	}
}
````
The `applySteering` method above takes the accelerations calculated by the steering behavior and the time step.
It updates position, linear velocity, orientation and angular velocity of the steering agent taking into account the flag specifying whether independent facing is enabled or not.


**IMPORTANT NOTES:**

- **Controlling top speed in character's update:** It's worth noting that the `applySteering` methods works out the linear velocity by adding the linear acceleration produced by the steering system.
Obviously, if the character keeps on accelerating, its speed will grow larger and larger. Most game characters have a maximum speed they can travel; they can't accelerate indefinitely.
In our implementation the current speed of the character (the length of the `linearVelocity` vector) is then checked regularly, and it's trimmed back if it exceeds the maximum speed.
Alternatively, maximum speed might be a result of applying a drag to slow down the character a little at each frame. Games that rely on physics engines typically include drag.
They don't need to check and clip the current velocity; the drag (applied in the update function) automatically limits the top speed.
- **Steering behaviors' structure:** All the steering behaviors provided by the framework have a similar structure. They always take as input the owner and additionally a minimal set 
of parameters required to accomplish their work. Some parameters are strictly behavior-specific, such as the target to pursue or evade, the path for a path following behavior, the ray configuration
for raycast obstacle avoidance, the forward offset of the wander circle, and so on.
Some other parameters, such as the maximum linear speed and acceleration, can be considered as character's properties or behavior's properties depending on the situation.
The `Limiter` interface allow you to decide where are those maximum values taken from. You can optionally specify a limiter for any steering behavior. If you don't provide one then the owner itself
will be used (notice that `Steerable` extends `Limiter`).

## Individual Behaviors ##

Unlike group behaviors, which we'll see later, individual behaviors take into account a very limited number of target agents, typically just one ore even none.
For instance, Wander and LookWhereYouAreGoing behaviors have no explicit target, while Interpose has two targets.
All other behaviors have a single target. 

### Seek and Flee ###

[Seek](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Seek.html) behavior moves
the owner towards the target position. Given a target, this behavior calculates the linear steering acceleration
which will direct the agent towards the target.

[Flee](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Flee.html) behavior does
the opposite of Seek. It produces a linear steering force that moves the agent away from a target position.

### Arrive ###

[Arrive](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Arrive.html) behavior moves
the agent towards a target position. It is similar to Seek but it attempts to arrive at the target position with a zero velocity.

Arrive behavior uses two radii. The [arrival tolerance](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Arrive.html#arrivalTolerance)
lets the owner get near enough to the target without letting small errors keep it in motion.
The [deceleration radius](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Arrive.html#decelerationRadius),
usually much larger than the previous one, specifies when the incoming character will begin to slow down.
The algorithm calculates an ideal speed for the owner. At the slowing-down radius, this is equal to its maximum speed. At the target point,
it is zero (we want to have zero speed when we arrive). In between, the desired speed is an interpolated intermediate value, controlled by
the distance from the target.

The direction toward the target is calculated and combined with the desired speed to give a target velocity.
The algorithm looks at the current velocity of the character and works out the acceleration needed to turn it into the target velocity.
We can't immediately change velocity, however, so the acceleration is calculated based on reaching the target velocity in a fixed time scale
known as [time to target](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Arrive.html#timeToTarget).
This is usually a small value; it defaults to 0.1 seconds which is a good starting point.

### Reach Orientation ###

[ReachOrientation](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/ReachOrientation.html) tries to
align the owner to the target. It pays no attention to the position or velocity of the owner or target.
This steering behavior does not produce any linear acceleration; it only responds by turning.

ReachOrientation behaves in a somewhat similar way to [Arrive](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Arrive.html)
since it tries to reach the target orientation and tries to have zero rotation when it gets there.
Like arrive, it uses two radii: [deceleration radius](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/ReachOrientation.html#decelerationRadius)
for slowing down and [align tolerance](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/ReachOrientation.html#alignTolerance)
to make orientations near the target acceptable without letting small errors keep the owner swinging.
Because we are dealing with a single scalar value, rather than a 2D or 3D vector, the radius acts as an interval.

Similarly to Arrive, there is a [time to target](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/ReachOrientation.html#timeToTarget)
that defaults to 0.1 seconds.

### Pursue and Evade ###

[Pursue](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Pursue.html) behavior produces
a force that steers the agent towards the evader (the target). Actually it predicts where an agent will be in time T and seeks
towards that point to intercept it. We did this naturally playing tag as children, which is why the most difficult tag players
to catch were those who kept switching direction, foiling our predictions.

The implementation performs the prediction by assuming the target will continue moving with the same velocity it currently has.
This is a reasonable assumption over short distances, and even over longer distances it doesn't appear too stupid. The
algorithm works out the distance between character and target and works out how long it would take to get there, at maximum
speed. It uses this time interval as its prediction lookahead. It calculates the position of the target if it continues to move
with its current velocity. This new position is then used as the target of a standard seek behavior.

If the character is moving slowly, or the target is a long way away, the prediction time could be very large. The target is
less likely to follow the same path forever, so we'd like to set a limit on how far ahead we aim. The algorithm has a
[maximum prediction time](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Pursue.html#maxPredictionTime)
for this reason. If the prediction time is beyond this, then the maximum time is used.

[Evade](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Evade.html) behavior is almost
the same as Pursue except that the agent flees from the estimated future position of the pursuer. Indeed, reversing the acceleration
is all we have to do.


### Face ###

[Face](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Face.html) behavior makes
the owner look at its target. It delegates to the [ReachOrientation](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/ReachOrientation.html)
behavior to perform the rotation but calculates the target orientation first based on target and owner position.
 
### Look Where You Are Going ###

[LookWhereYouAreGoing](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/LookWhereYouAreGoing.html)
behavior gives the owner angular acceleration to make it face in the direction it is moving. In this way the owner changes facing gradually,
which can look more natural, especially for aerial vehicles such as helicopters or for human characters that can move sideways.

This is a process similar to the [Face](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Face.html) behavior.
The target orientation is calculated using the current velocity of the owner. If there is no velocity, then the target orientation is set to the
current orientation. We have no preference in this situation for any orientation.

### Wander ###

[Wander](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Wander.html) behavior is
designed to produce a steering acceleration that will give the impression of a random walk through
the agent's environment. You'll often find it a useful ingredient when creating an agent's behavior.

![wander](https://cloud.githubusercontent.com/assets/2366334/3997408/1e029448-293f-11e4-8ab7-f35f2d3ce002.png)

There is a circle in front of the owner (where front is determined by its current facing direction) on which the target is
constrained. Each time the behavior is run, we move the target around the circle a little, by a random amount. Now there are 2
ways to implement wander behavior:
- The owner seeks the target, using the [Seek](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Seek.html)
behavior, and performs a [LookWhereYouAreGoing](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/LookWhereYouAreGoing.html)
behavior to correct its orientation.
- The owner tries to face the target in each frame, using the [Face](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Face.html)
behavior to align to the target, and applies full linear acceleration in the direction of its current orientation.

In either case, the orientation of the owner is retained between calls (so smoothing the changes in orientation). The angles
that the edges of the circle subtend to the owner determine how fast it will turn. If the target is on one of these extreme
points, it will turn quickly. The target will twitch and jitter around the edge of the circle, but the owner's orientation will
change smoothly.

Our implementation uses the second approach. However, if you don't use independent facing (i.e. you manually align owner's
orientation to its linear velocity on each time step), Face behavior is redundant. To prevent it from being executed just
set the [maximum angular acceleration](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Wander.html#maxAngularAcceleration) to zero.

This steering behavior can be used to produce a whole range of random motion, from very smooth undulating turns to wild
Strictly Ballroom type whirls and pirouettes depending on the size of the circle, its distance from the agent, and the amount
of random displacement each frame.

### Flow Field Following ###

The [FollowFlowField](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/FollowFlowField.html)
behavior produces a linear acceleration that tries to align the motion of the owner with the local tangent of a flow field.
The flow field defines a mapping from a location in space to a flow vector.

![followflowfield](https://cloud.githubusercontent.com/assets/2366334/4008930/a985046c-29dd-11e4-8ed4-44925cd59bc0.png)

This behavior can work in a predictive manner when [prediction time](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/FollowFlowField.html#predictionTime)
is greater than 0.

Any flow field can be used as the basis of this steering behavior, although it is sensitive to discontinuities in the field.
Flow fields can be used for simulating various effects, such as magnetic fields, an irregular gust of wind or the
meandering path of a river. They can be generated by a simple random algorithm, a path-finding algorithm, a Perlin noise or a
complicated image processing. And of course flow fields can be dynamic. The only limit is your imagination.

For instance, if your game level has many static obstacles, meaning ones that never move during the course of the game,
you can use flow fields to avoid them. Think of flow fields as a topographic map, with each of the obstacles being
mountain peaks and the space between the obstacles as valleys. Flow fields can be auto-generated by having a program
mark a map with maximum repelling forces directly around each obstacle, slowly lowering the force in a radiating pattern
as it marks the map farther away from the obstacle. Multiple obstacles close to each other would have an additive effect
on the overall repulsion force. In this way, characters would be auto-repelled from obstacles, because the ground around
each obstacle is covered with the strongest repulsion forces. This is also very low cost from a CPU perspective as an
avoidance technique, since it involves simple addition of the flow-field vector to the character velocity wherever the character
is on the game map, with no repulsion calculations necessary during runtime. This is what's called "offline AI data" because
all of this information is processed before the game ever runs.

Pushing this concept to the very limit, you could even experiment with having lots of attractors and repulsors in your game level
and having character movement mostly controlled by these. Characters are attracted to their goals and repelled from obstacles,
for example. Despite being ostensibly simple, this approach is full of traps for the unwary.

[This article](http://gamedevelopment.tutsplus.com/tutorials/understanding-goal-based-vector-field-pathfinding--gamedev-9007)
shows an interesting approach combining flow field following and pathfinding, known as *vector field pathfinding*. This
technique is very efficient when you have to deal with a lot of agents.


### Path Following ###

[FollowPath](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/FollowPath.html) behavior
produces a linear acceleration that moves the agent along the given path. First it calculates the agent location based on the
specified prediction time. Then it works out the position of the internal target based on the location just calculated and the
shape of the path. It finally uses [Seek](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Seek.html)
behavior to move the owner towards the internal target position. However, if the path is open [Arrive](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Arrive.html)
behavior is used to approach path's extremities when they are far less than the [deceleration radius](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Arrive.html#decelerationRadius)
from the internal target position.

Like FollowFlowField, this behavior works in a predictive manner when [prediction time](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/FollowPath.html#predictionTime)
is greater than 0.

![predictivepathfollowing](https://cloud.githubusercontent.com/assets/2366334/4009031/f1423eae-29de-11e4-916d-05ac9b3ba414.png)

For complex paths with sudden changes of direction the predictive behavior can appear smoother than the
non-predictive one. However, predictive path following has the downside of cutting corners when some sections of the path
come close together. This cutting-corner attitude can make the character miss a whole section of the path. This might not
be what you want if, for example, the path represents a patrol route.

### Interpose ###

[Interpose](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Interpose.html) behavior
produces a steering force that moves the owner to a point along the imaginary line connecting two other agents. A bodyguard
taking a bullet for his employer or a soccer player intercepting a pass are examples of this type of behavior.

![interpose](https://cloud.githubusercontent.com/assets/2366334/3997417/2f2c26ee-293f-11e4-84b8-8f1376d88085.png)

Like Pursue, the owner must estimate where the two agents are going to be located at a time T in the
future. It can then steer toward that position using the [Arrive](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Arrive.html)
behavior. But how do we know what the best value of T is to use? The answer is, we don't, so we make a calculated guess instead.

The first step is to determine a point along the imaginary line connecting the positions of the agents at the current time
step. This point is found taking into account the [interposition ratio](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Interpose.html#interpositionRatio),
a number between 0 and 1 where 0 is the position of the first agent (agentA) and 1 is the position of the second agent (agentB).
Values in between are interpolated intermediate locations.

Then the distance from this point is computed and the value divided by the owner's maximum speed to give the time T
required to travel the distance.

Using the time T, the agents' positions are extrapolated into the future. The target position in between of these
predicted positions is determined and finally the owner uses the Arrive behavior to steer toward that point.

### Match Velocity ###

So far we have encountered some behaviors that try to match position with a target, such as Seek and Flee.
This behavior does the same with velocity. But if you stop and think you'll realize that we have already seen a behavior
that tries to match a velocity. Arrive calculates a target velocity based on the distance to its target then tries
to achieve the target velocity. MatchVelocity is a stripped down version of Arrive indeed.

Unfortunately, MatchVelocity on its own is seldom useful. It could be used to make a character mimic the motion of a
target, but normally this isn't very useful. Where it does become handy is when a character needs to jump. 
Actually, Jump behavior is implemented as a subclass of MatchVelocity. That's pretty much why the framework provides
MatchVelocity behavior.

### Jump ###

Jumps are risky by nature. Unlike other steering behaviors, they can fail and such a failure may
make it difficult or impossible to recover. At the very limit, a failed jump may kill the character.

For example, if a character decides to make a jump between two platforms, the steering controller needs to make
sure that the character is moving at the correct speed and in the correct direction and that the jump action is
executed at the right moment, or at least not too late.  Slight perturbations in the character's movement,
caused by the blast wave of an explosion, for example, can lead to the character dramatically missing the landing
spot.

By contrast, standard steering behaviors effectively distribute their thinking over time. Each decision they make
is very simple, but because they are constantly reconsidering the decision, the overall effect is competent.
Jumping is a one-time, fail-sensitive decision.

A good approach to address a jump is to use a *jump descriptor* object that pairs a *jump point* with a *landing pad*.
These are special locations that need to be manually placed by the game level designer. 
Here are the steps to achieve the jump:

1. **Deciding to make a jump.** The character may use some pathfinding system to determine that it needs to be
on the other side of the gap, or else it may be using a simple steering behavior and be drawn toward the ledge.
2. **Recognizing which jump to make.** This will normally happen automatically if you're using pathfinding
(see jump links, below). If you're using a local steering behavior, then you have to determine that a
jump is ahead in enough time to make it. A reasonable lookahead is required.
3. **Taking a run up.** It's time for the Jump steering behavior to take over:
  - it first calculates the velocity required to land exactly on the landing pad when taking off from the jump point.
  - then it uses the calculated velocity as the target of the MatchVelocity behavior to take a run up toward
    the jump point with the correct speed and direction.
4. **And finally jumping.** As the character crosses onto the jump point, a jump action is executed, and the
character becomes airborne.

**IMPORTANT NOTES:**
- **Gravity and Drag:** The Jump behavior provided by the framework is well suited to working with a physics engine
because you can just apply the proper force to make the character jump and the gravity will make it land. However,
there are a couple of limitations you have to take into account:
  * Jump behavior expects that gravity is acting along one axis only. Supporting gravity in multiple directions causes
    significant problems in the mathematics that are best avoided, especially since the vast majority of games are covered.
  * Jump behavior assumes that during the airborne trajectory there is no drag, air resistance, damping, friction or what else.
    Since drag is usually non-existent or negligible for jump trajectory calculations this is, once again, the most common
    situation. If the character is affected by drag while it moves on the ground, then the developer is responsible for
    removing and restoring drag when the character jumps and lands respectively.
- **Taking Control:** When using Jump behavior as part of an entire steering system, it is important to make sure it can take complete
control of the character. If Jump behavior is combined with other steering behaviors using a blending algorithm,
then it will almost certainly fail eventually. A character that is avoiding an enemy at a tangent to the jump will
have its trajectory skewed. It either will not arrive at the jump point (and therefore not take off) or will jump
in the wrong direction and plummet.
- **Understanding Parameters:** Jump behavior parameters may be hard to set properly. It's very important that you understand
  their meaning and the role they play. 
  * *Run up length*: Ideally the longer the run up, the more accurate the velocity (speed and direction) toward the jump point.
    However, a long run up might increase the chance to encounter problematic obstacles.
  * *Position and velocity tolerances*: To make the jump the character has to reach a position close enough to the jump point
    with a certain accuracy in relation to direction and speed (the velocity vector). Position tolerance determines the
    dimension of the take-off pad and together with the velocity tolerance they determine the dimension of the landing pad.
    Tolerance values that are too low can lead to the character missing the jump point and the jump would not be executed.
    Tolerance values that are too high can lead to the character jumping too early or with incompetent velocity, so missing
    the landing pad. If the character must absolutely jump it might be more convenient to use large tolerance values
    and then, if necessary, correct the airborne planar velocity right before making the jump. This will ensure that the
    jump is performed possibly at the cost of a less realistic visual effect.
  * *Max Vertical Velocity*: It's the maximum vertical component of the airborne velocity, where "vertical" stands for the
    axis where gravity operates. Usually, but not necessarily, this will be the vertical axis. The greater the maximum vertical
    velocity, the higher the jump. Its value contributes to determine if the jump is achievable.
  * *Jump Achievability*: If the jump is unachievable the character won't even enter the run up phase. Actually, the Jump behavior
    uses airborne trajectory prediction to determine jump achievability. The following parameters contribute to the calculation:
    the difference between take-off and landing points, the maximum linear speed, the maximum vertical velocity, and the gravity. 
- **Jump Links:** Often developers incorporate jump descriptors into their pathfinding framework. As part of the
pathfinding system, we create a network of locations in the game. The connections that link locations have information
stored with them (the distance between the locations in particular). We can simply add jumping information to this
connection. A connection between two nodes on either side of a gap is labeled as requiring a jump. At runtime, the
link can be treated just like a jump descriptor object (i.e., a pair made up of jump point and landing pad), and
the Jump behavior can be applied to carry out the jump.
- **Hole Fillers:** Another approach used by several developers allows characters to choose their own jump points.
The level designer fills holes with an invisible object, labeled as a jumpable gap. The character steers based on a
jump detector, which is a special variation of the raycast obstacle avoidance behavior described below. This behavior
treats collisions with the jumpable gap object differently from collisions with walls. Rather than trying to avoid the wall,
it moves toward it at full speed. At the point of collision (i.e., the last possible moment that the character is on
the ledge), it executes a jump action and leaps into the air. This approach has great flexibility; characters are
not limited to a particular set of locations from which they can jump. In a room that has a large chasm running through
it, for example, the character can jump across at any point because the jump detector will execute the jump across
automatically. There is no need for separate jump points on each side of the chasm. The same jumpable gap object works
for both sides. 
However, this approach suffers from the problem of sensitivity to landing areas. With no target velocity, or notion
of where the character wants to land, it will not be able to sensibly work out how to take off to avoid missing a
landing spot. This technique is ideal when the jumpable gaps are surrounded by ample take off and landing space,
making the chance of failing the jump negligible. This means that you have to design levels that don't reveal the
weaknesses in the approach.


## Group Behaviors ##

Group behaviors are steering behaviors that take into consideration some or all of the other objects in the game world.
To determine the steering acceleration for a group behavior, a character will consider all (or some) other characters
within its immediate area, also known as [Proximity](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Proximity.html).

![proximity](https://cloud.githubusercontent.com/assets/2366334/3997424/46684950-293f-11e4-92b7-c94f6cc10dd8.png)

Typically (but not necessarily) different group behaviors share the same proximity for a given owner. This allows you to
combine group behaviors so as to get a more complex behavior also known as emergent behavior. Emergent behavior is behavior
that looks complex and/or purposeful to the observer but is actually derived spontaneously from fairly simple rules.
The lower-level agents following the rules have no idea of the bigger picture; they are only aware of themselves and maybe a few of
their neighbors. A typical example of emergence is flocking behavior which is a combination of three group behaviors:
separation, alignment, and cohesion. The three behaviors are typically combined through a BlendedSteering (see below).
This works okay but, because of the limited view distance of a character, it's possible for an agent to become isolated from its flock.
If this happens, it will just sit still and do nothing. To prevent this from happening, you usually add in the wander behavior too.
This way, all the agents keep moving all the time. Tweaking the magnitudes of each of the contributing behaviors will give you
different effects such as shoals of fish, loose swirling flocks of birds, or bustling close-knit herds of sheep.

Before a steering acceleration can be calculated for a combination of group behaviors, the neighbors must be determined and
processed. This is done by the [findNeighbors](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Proximity.html#findNeighbors(ProximityCallback))
method and its callback argument.

**IMPORTANT NOTES:**

- Sharing a Proximity instance among group behaviors having the same owner can save a little time determining the
neighbors only once from inside the findNeighbors method. Especially, Proximity implementation classes can check
the [frameId](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/AIUtils#getFrameId()) of the current frame
in order to calculate neighbors only once per frame.
- If you want to make sure a Proximity doesn't use as a neighbor a given agent, for example the evader or the
owner itself, you have to implement a callback that prevents it from being considered by returning `false` from the method
[reportNeighbor](@link http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Proximity.ProximityCallback.html#reportNeighbor(Steerable)).
- If there is some efficient way of pruning potential neighbors before they are processed, the overall performance in time
will improve. Spatial data structures such as multi-resolution maps, quad-trees, oct-trees, and binary space partition (BSP)
trees can be used to get potential neighbors more efficiently. Spatial partitioning techniques are crucial when you have to
deal with lots of agents. Especially, if you're using Bullet or Box2d in your game, it's recommended to implement proximities
that exploit their methods to query the world. Both Bullet and Box2d internally use some kind of spatial partitioning.


### Separation ###

[Separation](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Separation.html)
is a group behavior producing a steering acceleration repelling from the other neighbors which are the agents within the
immediate area defined by the given [Proximity](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/Proximity.html).
The acceleration is calculated by iterating through all the neighbors, examining each one. The vector to each agent under
consideration is normalized, divided by the distance to the neighbor, and accumulated.

![separation](https://cloud.githubusercontent.com/assets/2366334/3997438/5f2a0adc-293f-11e4-833b-a9ad4439ad91.png)


### Alignment ###

[Alignment](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Alignment.html)
is a group behavior producing a linear acceleration that attempts to keep the owner aligned with the agents in its
immediate area defined by the given [Proximity](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/Proximity.html).
The acceleration is calculated by first iterating through all the neighbors and averaging their normalized linear
velocity vectors. This value is the desired direction, so we just subtract the owner's normalized linear velocity
to get the steering output.

![alignment](https://cloud.githubusercontent.com/assets/2366334/3997427/4fd47edc-293f-11e4-8df1-8f60d2311ce1.png)

Cars moving along roads demonstrate Alignment type behavior. They also demonstrate Separation as they try to
keep a minimum distance from each other.

### Cohesion ###

[Cohesion](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Cohesion.html)
is a group behavior producing a linear acceleration that attempts to move the agent towards the center of mass
of the agents in its immediate area defined by the given [Proximity](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/Proximity.html).
The acceleration is calculated by first iterating through all the neighbors and averaging their position vectors.
This gives us the center of mass of the neighbors, the place the agents wants to get to, so it seeks to that position.

Also, the implementation always returns a normalized linear acceleration (or zero). This is not a problem since usually
you blend it with other group behaviors like Separation and Alignment so you can give it a proper weight, see
BlendedSteering.

![cohesion](https://cloud.githubusercontent.com/assets/2366334/3997430/57ea14ce-293f-11e4-82fe-e6170c66b6f2.png)

A sheep running after its flock is demonstrating cohesive behavior. Use this behavior to keep a group of agents together.

### Hide ###

[Hide](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Hide.html) behavior
attempts to position a owner so that an obstacle is always between itself and the agent (the hunter) it's trying
to hide from. First the distance to each of these obstacles is determined. Then the owner uses the arrive behavior to steer
toward the closest one. If no appropriate obstacles can be found, no steering is returned.

![hide](https://cloud.githubusercontent.com/assets/2366334/3997420/36a31180-293f-11e4-9816-31cb27a6b629.png)

You can use this behavior not only for situations where you require a non-player character (NPC) to hide from the player, like
find cover when fired at, but also in situations where you would like an NPC to sneak up on a player. For example, you can
create an NPC capable of stalking a player through a gloomy forest, darting from tree to tree.

It's worth mentioning that since this behavior can produce no steering acceleration it is commonly used with
[PrioritySteering](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/PrioritySteering.html).
For instance, to make the owner go away from the target if there are no obstacles nearby to hide
behind, just use Hide and Evade behaviors with this priority order.

There are a few interesting modifications you might want to make to this behavior:
- With [FieldOfViewProximity](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/proximities/FieldOfViewProximity.html)
you can allow the owner to hide only if the target is within its field of view.
This tends to produce unsatisfactory performance though, because the owner starts to act like a child hiding from monsters beneath
the bed sheets, something like "if you can't see it, then it can't see you" effect making the owner look dumb. This can be
countered slightly though by adding in a time effect so that the owner will hide if the target is visible or if it has seen the
target within the last N seconds. This gives it a sort of memory and produces reasonable-looking behavior.
- The same as above, but this time the owner only tries to hide if the owner can see the target and the target can see the owner.
- It might be desirable to produce a force that steers the owner so that it always favors hiding positions that are to the
side or rear of the pursuer. This can be achieved easily using the dot product to bias the distances returned from the method
 [getHidingPosition](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/Hide.html#getHidingPosition).
- At the beginning of any of the methods a check can be made to test if the target is within a "threat distance" before
proceeding with any further calculations. If the target is not a threat, then the method can return immediately with zero steering.

### Collision Avoidance ###

[CollisionAvoidance](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/CollisionAvoidance.html)
behavior steers the owner to avoid obstacles lying in its path. An obstacle is any object that can be approximated by a circle
(or sphere, if you are working in 3D).

The implementation uses collision prediction working out the closest approach of two agents and determining if their distance
at this point is less than the sum of their bounding radius. For avoiding groups of characters, averaging positions and
velocities do not work well with this approach. Instead, the algorithm needs to search for the character whose closest approach
will occur first and to react to this character only. Once this imminent collision is avoided, the steering behavior can then
react to more distant characters.

This algorithm works well with small and/or moving obstacles whose shape can be approximately represented by a center and a
radius.

### Raycast Obstacle Avoidance ###

With the [RaycastObstacleAvoidance](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/RaycastObstacleAvoidance.html)
the moving agent (the owner) casts one or more rays out in the direction of its motion. If these rays collide with an obstacle,
then a target is created that will avoid the collision, and the owner does a basic seek on this target. Typically, the rays
extend a short distance ahead of the character (usually a distance corresponding to a few seconds of movement).

![raycastobstacleavoidance](https://cloud.githubusercontent.com/assets/2366334/3997628/fc884af4-2940-11e4-9b7a-58a023c511cc.png)

This behavior is especially suitable for large-scale obstacles like walls.

You should use the [RayConfiguration](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/RaycastObstacleAvoidance#RayConfiguration.html)
more suitable for your game environment. Some basic ray configurations are provided by the framework:
[SingleRayConfiguration](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/rays/SingleRayConfiguration.html),
[ParallelSideRayConfiguration](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/rays/ParallelSideRayConfiguration.html),
and [CentralRayWithWhiskersConfiguration](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/rays/CentralRayWithWhiskersConfiguration.html).
There are no hard and fast rules as to which configuration is better. Each has its own particular idiosyncrasies. A central ray with
short whiskers is often the best initial configuration to try but can make it impossible for the character to move down tight passages.
The single ray configuration is useful in concave environments but grazes convex obstacles. The parallel configuration works well in
areas where corners are highly obtuse but is very susceptible to the corner trap.

#### The corner trap ####
All the basic configurations for multi-ray obstacle avoidance can suffer from a crippling problem
with acute angled corners (any convex corner, in fact, but it is more prevalent with acute angles). 

![cornertrap](https://cloud.githubusercontent.com/assets/2366334/3997720/bda7848e-2941-11e4-92aa-7e5f230dcfcb.png)

Consider the above character with two rays that is going towards a corner. As soon as its left ray is colliding with the wall near
the corner, the steering behavior will turn it to the left to avoid the collision. Immediately, the right ray will then be colliding
the other side of the corner, and the steering behavior will turn the character to the right. The character will repeatedly collide
both sides of the corner in rapid succession. It will appear to home into the corner directly, until it slams into the wall. It will
be unable to free itself from the trap.

The fan structure, with a wide enough fan angle, alleviates this problem. Often, there is a trade-off, however, between
avoiding the corner trap with a large fan angle and keeping the angle small to allow the character to access small passages. At
worst, with a fan angle near PI radians, the character will not be able to respond quickly enough to collisions detected on its
side rays and will still graze against walls. There are two approaches that work well and represent the most practical
solutions to the problem:
- **Adaptive fan angles:** If the character is moving successfully without a collision, then the fan angle is narrowed. If
collision is detected, then the fan angle is widened. If the character detects many collisions on successive frames, then the
fan angle will continue to widen, reducing the chance that the character is trapped in a corner.
- **Winner ray:** If a corner trap is detected, then one of the rays is considered to have won, and the collisions
detected by other rays are ignored for a while.

It seems that the most practical solution is to use adaptive fan angles, with one long ray cast and two shorter whiskers.


## Combining Steering Behaviors ##

Individually, steering behaviors can achieve a good degree of movement sophistication. However, a moving character
usually needs more than one steering behavior. It needs to reach its target, avoid collisions with other characters,
tend toward safety as it moves, and avoid bumping into walls. Wall and obstacle avoidance can be particularly
difficult to get when working with other behaviors. In addition, some complex steering, such as flocking
and formation motion (not implemented yet), can only be accomplished when more than one steering behavior is active
at once. This section explains you how to accomplish this combination.

### Blended Steering ###

[BlendedSteering](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/BlendedSteering.html)
is a combination behavior that simply sums up all the active behaviors, applies their weights, and truncates the result before
returning. There are no constraints on the blending weights; they don't have to sum to one, for example, and rarely do. Don't
think of BlendedSteering as a weighted mean, because it's not.

With BlendedSteering you can combine multiple behaviors to get a more complex behavior. It can work fine, but the
trade-off is that it comes with a few problems:
- Since every active behavior is calculated every time step, it can be a costly method to process.
- Behavior weights can be difficult to tweak. There have been research projects that have tried to evolve the steering
weights using genetic algorithms or neural networks. Results have not been encouraging, however, and manual experimentation
still seems to be the most sensible approach.
- It's problematic with conflicting forces. For instance, a common scenario is where an agent is backed up against a wall by
several other agents. In this example, the separating forces from the neighboring agents can be greater than the repulsive
force from the wall and the agent can end up being pushed through the wall boundary. This is almost certainly not going to be
favorable. Sure you can make the weights for the wall avoidance huge, but then your agent may behave strangely next time it
finds itself alone and next to a wall.

### Priority Steering ###

[PrioritySteering](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/ai/steer/behaviors/PrioritySteering.html)
behavior iterates through the active behaviors and returns the first non zero steering. It makes
sense since certain steering behaviors only request an acceleration in particular conditions. Unlike Seek or
Evade, which always produce an acceleration, RaycastObstacleAvoidance, CollisionAvoidance, Separation, Hide and Arrive will
suggest no acceleration in many cases. But when these behaviors do suggest an acceleration, it is unwise to ignore it.
An obstacle avoidance behavior, for example, should be honored immediately to avoid the crash.

Typically the behaviors of a PrioritySteering are arranged in groups with regular blending weights, see BlendedSteering.
These groups are then placed in priority order to let the steering system consider each group in turn.
It blends the steering behaviors in the current group together. If the total result is very small (less than some small,
but adjustable, parameter), then it is ignored and the next group is considered. It is best not to check against zero directly,
because numerical instability in calculations can mean that a zero value is never reached for some steering behaviors.
Using a small constant value (conventionally called epsilon) avoids this problem. When a group is found with a result that isn't
small, its result is used to steer the agent.

For instance, a pursuing agent working in a team may have three priorities:
- a collision avoidance group that contains behaviors for obstacle avoidance, wall avoidance, and avoiding other characters.
- a separation behavior used to avoid getting too close to other members of the chasing pack.
- a pursuit behavior to chase the target.

If the character is far from any interference, the collision avoidance group will return with no desired acceleration.
The separation behavior will then be considered but will also return with no action. Finally, the pursuit behavior will be
considered, and the acceleration needed to continue the chase will be used. If the current motion of the character is perfect
for the pursuit, this behavior may also return with no acceleration. In this case, there are no more behaviors to consider, so
the character will have no acceleration, just as if they'd been exclusively controlled by the pursuit behavior.

In a different scenario, if the character is about to crash into a wall, the first group will return an acceleration that will
help avoid the crash. The character will carry out this acceleration immediately, and the steering behaviors in the other
groups won't be considered.

Usually PrioritySteering gives you a good compromise between speed and accuracy.
