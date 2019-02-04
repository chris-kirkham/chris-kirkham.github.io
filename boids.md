# boids
#### An implementation of the Boids algorithm (Reynolds, C. W. (1987) Flocks, Herds, and Schools: A Distributed Behavioral Model) for Unity.

<div>
  <img src="images/boids/README_1.gif" style="float: center; width: 50%; margin-left: 1%; margin-right: 1%; margin-bottom: 0.5em;"/>
  <img src="images/boids/README_2.gif" style="float:center; width: 50%; margin-left: 1%; margin-right: 1%; margin-bottom: 0.5em;"/>
  <img src="images/boids/README_3.gif" style="float:center; width: 50%; margin-left: 1%; margin-right: 1%; margin-bottom: 0.5em;"/>
  <p style="clear: both;"></p>
</div>

## About
This is a Unity implementation of Craig Reynold's *Boids* algorithm (<http://www.cs.toronto.edu/~dt/siggraph97-course/cwr87/>). Essentially, the system is formed of several "boid" particles which react dynamically to each other and to the environment in a manner similar to a flock of birds or a school of fish.

## Controls
<p> WASD - move camera <br> 
Hold RMB + move mouse - rotate camera <br>
LMB - toggle mouse follow on/off <br>
Z - spawn boid <br>
X - delete boid </p>

## How it works
All individual boid behaviour is handled in *BoidBehaviour.cs*; boids are spawned from the *BoidController* game object, which contains the scripts *BoidSpawner.cs* and *BoidCollectiveController.cs*. User input is handled in the singleton class *ControlInputs.cs*.

### Attention range
Each boid has a sphere around it which represents its range of attention - if another boid overlaps this sphere, it is added to a list of boids to react to on that frame. To save performance, the boids can be set to store and react to only the closest (up to) *n* boids to it, even if there are more within its attention range. 

A boid's attention sphere may be set to be of dynamic size; it will grow until it overlaps *n* other boids, then shrink (if necessary) to remain at the minimum size which still overlaps *n* boids. In practice, this allows boids to group together in sub-flocks of a set number (not realistic behaviour in terms of actual birds, but may be useful in certain situations).

### Reaction to other boids
Each boid attempts to:
* Stay a certain distance from other boids
* Match velocity with that of nearby boids
* Move towards the centre of nearby boids

In this implementation, all boids are the same (small) size and shape, so the maintenance of distance between boids is simply a case of, for each boid in the flock, averaging the difference between its position and that of the boids in its attention range. Matching velocities and centring the boid is also achieved through averaging nearby boids' velocity/position vectors.

### Obstacle avoidance
Because a boid may want to begin avoiding collisions from a distance, "planning ahead" for obstacles rather than simply reacting when one is close, it uses a ray cast a set distance in the direction of a boid's target vector (which is either ahead of it in its current movement direction or the position of a follow target); avoidance behaviour is undertaken if this ray hits an obstacle.

Unlike boids, obstacles can be any size and shape, so the method used for boid avoidance will not work. We could use an obstacle's bounding box to work out its size and therefore where to move to avoid it, but this would cause poor behaviour for obstacles whose shape does not match their bounds - objects with holes in them, for example. Therefore, the avoidance behaviour uses an incremental ray/line casting method: rays are cast at increasingly wide angles from a boid's target direction until a vector is found (or until the number of rays cast reaches a predetermined maximum) which allows the boid to avoid the obstacle. If more than one avoid vector is found, the boid chooses the one which is closest to its target vector in order to minimise unnecessary directional change.

### Cursor follow
The boids may follow a 3D target controlled by the user's mouse input, and will move around obstacles to reach this target.


###### Christopher Kirkham, 2019
