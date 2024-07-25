---
layout: post
title:  "Simulating rockets"
subtitle: "Thrust, gravity, and drag"
date:   2024-07-24 12:00:15 -0500
categories: [python, space]
background: '/assets/images/rocket.jpg'
---

## Intro

Space is fascinating, and getting to space is one of the most impressive pieces of engineering, with so many things having to go right. I think it would be pretty interesting to simulate all the physics involved in going to space, maybe even in simulating the up coming [Artemis](https://www.nasa.gov/humans-in-space/artemis/) missions to the moon, or [going to mars](https://science.nasa.gov/planetary-science/programs/mars-exploration/). There are a lot of pieces involved, from [how the engines work](https://www.sciencelearn.org.nz/resources/393-types-of-chemical-rocket-engines), to cosmic rays [flipping bits](https://www.scienceabc.com/innovation/what-are-bit-flips-and-how-are-spacecraft-protected-from-them.html) on the onboard computers. While I would love to simulate all of these little details one day, for now we'll have to start with something a little more simple; the trajectory of a rocket.

## Baby steps

To start off we'll simulate a model rocket, only capable of going a few hundred meters up. This simulation will be in only two dimensions, and we'll only account for three forces on the rocket: thrust from the engines, gravity, and the drag created from air resistance. We'll also not be accounting for any navigation controls of the rocket, instead just picking a constant orientation for the rocket.

## Our rocket

The rocket we'll be using today, which I selected more or less randomly, will be the [Estes' Long Ranger](https://estesrockets.com/products/long-ranger) with a [C6-5 engine](https://estesrockets.com/products/c6-5-engines). According to Estes, this should be capable of reaching an altitude of about 335 meters, is 65.8 centimeters longs, 22 millimeters in diameter, and weighs 48 grams. The C6 engine adds another 24.1 grams in weight, and burns for 1.6 seconds. This is a fairly average model rocket, that anyone might launch if they are new to the hobby.

## Thrust

There are a few different ways normally used to measure the force provided from a rocket engine. First we have the average thrust of the rocket. If you multiply that by the thrust duration, you get the total impulse, or the total amount of thrust the rocket produces. For our engine the total impulse is 10 newton-seconds (N-sec). We can get the specific impulse of the rocket by dividing the total impulse by the weight of the propellent, this will help us determine how efficient the rocket is. Our engine's propellent weighs 12.2g, giving us a specific impulse of 0.82 seconds.

Now, while these numbers are useful, we would only be able to simulate a constant average thrust with them. However the thrust put out by rocket engines tends to vary over time, especially solid propellent ones like ours. To simulate that, we will need the thrust curve of the engine. If we were building our own rocket, we would need to preform static fire tests to figure out this curve. Thankfully we are using standard hobby engines, so I was able to find the curve [here](https://www.thrustcurve.org/motors/Estes/C6/). ![](/assets/posts//2024-07-24/thrust_curve.png)

I decided to download the 'RASP' format of this file. Which consists of some comments, then a header row, the space separated values mapping a time (in seconds) to a value for thrust (in Newtons). We'll assume it is a straight line between any two points. I created a simple class to read this file and give us the thrust for a given time:
```python
class ThrustCurve:
    def __init__(self, path) -> None:
        f = open(path)
        lines = f.readlines()
        no_comments = filter(lambda x: not x.startswith(';'), lines)
        no_header = list(no_comments)[1:]
        pairs = list(map(lambda x: x.split(), no_header))
        pairs_numbers = list(map(lambda x: [float(x[0]),float(x[1])], pairs))

        # Add the initial point
        pairs_numbers.insert(0, [0.0,0.0])
        self.data = pairs_numbers

    # get how much the current thrust is based on time since ignition
    def get_thrust(self, t):
        if t <= 0.0 or t > self.data[-1][0]:
            return 0.0
        
        start = [0,0]
        end = self.data[-1]
        for idx, x in enumerate(self.data):
            if x[0] >= t:
                end = x
                if(idx > 0):
                    start = self.data[idx -1]
                else:
                    start = [0.0, 0.0]
                break
        
        print(start[0])
        print(end[0])
        slope = (end[1] - start[1]) / (end[0] - start[0])
        offset = start[1] - (slope * start[0])

        return (slope * t) + offset
```
## Drag
<img src="/assets/posts/2024-07-24/ru.webp" alt="RuPaul" width="400"/>
We have to do a little more math to figure out the drag force applied to our rocket. [According to NASA](https://www.grc.nasa.gov/www/k-12/VirtualAero/BottleRocket/airplane/rktcodn.html) the drag force *D = C<sub>d</sub> (r V<sup>2</sup> / 2)A*. Where *C<sub>d</sub>* is the coefficient of drag, *r* is the air density, *V* is the velocity, and *A* is the reference area. NASA says *C<sub>d</sub>* for model rockets is about 0.75, so we'll use that. According to wikipedia the air density at sea level is 1.225 kg/m<sup>3</sup>, and while this would change with altitude, for now we are going to hold it constant, since our rocket wont be getting super high off the ground. Lastly we'll assume the area of a rocket is just the area of a circle with radius equal to the radius of our rocket.

Putting that into code we get:
```python
def drag_force(self, V):
    Cd = 0.75
    area = pi * (self.diameter/2)**2
    mag = self.air_density * vector_mag(V)**2 * Cd * area
    direction = -1 * vector_hat(V)
    return mag * direction
```

Note that V is a 2d vector, and that the direction of the drag force is the opposite direction of travel. I have that and some related helper function you can find [here](https://github.com/ShadowRonin/rocket-trajectory-2/blob/main/utils/vector.py).

## Gravity

While we calculated the forces for drag and thrust, for gravity we will be directly calculating the acceleration. Now the gravity of earth is normally stated as 9.8 m/s<sup>2</sup> towards the center of the earth. That might be good enough for now, but I went a little beyond to calculate what it would be no matter how high above earth you are. The calculations [per wikipedia](https://en.wikipedia.org/wiki/Gravity_of_Earth#Altitude) for this are g<sub>h</sub> = g<sub>0</sub> (R<sub>e</sub> / (R<sub>e</sub> + h))<sup>2</sup> where:
- g<sub>h</sub> is the acceleration of gravity at the given altitude
- R<sub>e</sub> is the radius of the earth (12,756,000 m)
- g<sub>0</sub> is standard gravity (9.8 m/s<sup>2</sup>)

For this we will assume the earth is a perfect sphere, and exists directly below the point (0,0) such that if the rocket starts at (0,0) it will be resting on top of the earth. In code that will give us: 
```python
def calc_gravity(self, P):
    height_above_earth = vector_mag(P - self.center_of_earth) - self.earth_radius
    mag = self.gravity * np.square(self.earth_radius / (self.earth_radius + height_above_earth))
    direction = vector_hat(self.center_of_earth - P)
    return mag * direction
```
Where P is the correct position of the rocket as a vector.

## Results

Now lets put this all together. To find the trajectory of the rocket we need to solve some integrals. I am going to leave off explaining the [exact math involved](http://www.aerostudents.com/courses/rocket-motion-and-reentry-systems/RocketMotionSummary.pdf) as there are [plenty](https://ntrs.nasa.gov/api/citations/19680008648/downloads/19680008648.pdf) of [places](https://openrocket.info/documentation.html) that do so better than I can. Just know that we have to solve some integrals, as velocity, acceleration (especially with drag), and position are all mingled together.

Conveniently [scipy](https://scipy.org/) has the [solve_ivp](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html#solve-ivp) function, which will allow us to figure this out. First we have to create the function that does our calculations, given a time (t) and the previous state:
```python
    def slope_func(self, t, values, args):
        x,y,vx,vy = values
        P = Vector(x,y)
        V = Vector(vx,vy)

        # Calculate mass, as it changes depending on how much propellent we've burned
        if t <= 0.0:
            mass = self.total_weight
        elif t >= self.burn_duration:
            mass = self.total_weight - self.prop_weight
        else:
            mass = self.total_weight - (self.prop_weight * (t / self.burn_duration))

        f_drag = self.drag_force(V)
        f_thrust = self.thrust_force(t, P)

        a_gravity = self.calc_gravity(P)

        # Calculate acceleration by dividing the force by the mass
        a_drag = f_drag / mass
        a_thrust = f_thrust / mass

        # Total acceleration
        A = a_drag + a_gravity + a_thrust

        # Dont move if we do not have positive thrust before liftoff
        # Sometimes the engine can take a moment to provide enough thrust
        # And we dont want to fall through the earth
        if not self.we_have_liftoff:
            if A.y <= self.gravity:
                A = Vector(0.0, 0.0)
            else:
                self.we_have_liftoff = True

        # Return our derivatives
        return vx, vy, A.x, A.y
```

Now lets add a function to call `solve_ivp` and return our results:
```python
    def simulate(self, num=101):
        t_0 = 0.0
        t_max = 60.0 # only run for 1 minute

        solved = solve_ivp(
            self.slope_func,
            [t_0, t_max],
            [0.0, 0.0, 0.0, 0.0], # starting values
            dense_output=True,
            events=[collide_with_earth], # this stops the sim earlier if we collide with earth
            args=[self]
            )
        
        y = solved.pop("y")
        t = solved.pop("t")
        
        # use the solved version to get results at consistent spacings
        # this will let allow the results to appear smoother in graphing
        t_final = t[-1]
        t_array = np.linspace(t_0, t_final, num)
        self.log_telemetry = True
        y_array = solved.sol(t_array)
        self.log_telemetry = False

        results = pd.DataFrame(y_array.T, index=t_array,
                        columns=['x','y','vx','vy'])
        
        return results
```

We can now use [matplotlib](https://matplotlib.org/3.5.3/index.html) to graph the results! As you can see below, if we launch our rocket at an angle 20 degrees down to the right of vertical, we reach an altitude of 219 meters and travel 110 meters downrange!

![](/assets/posts//2024-07-24/20-deg.png)



If we were to launch our rocket straight up, then we achieve a max altitude of 243 meters, which is a little off from the 335m max Estes says this rocket can fly. We have some work to do to improve our simulation, the biggest issue is probably our simplistic drag calculation. If the rocket is well designed, the coefficient of drag could be lower then what we used, allowing it to fly higher.

![](/assets/posts//2024-07-24/0-deg.png)

You'll also note that even flying vertically, we move a (extremely) tiny amount to the side. I believe this to just be the result of floating point errors when calculating the thrust vectors. In the future I might look into different ways to mitigate these errors, though it will probably be impossible to completely eliminate them.


## Summary

In all, we've now calculated the most basic forces on our rocket and plotted its trajectory. It's a good start for our little simulation. The next main goal will be to simulate a larger rocket going into orbit. First though, we need to get some more data. In my next post I'll be collecting some additional telemetry so we can graph our forces, acceleration (including gravity!), and mass over time. 