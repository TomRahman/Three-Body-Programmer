[README.md](https://github.com/user-attachments/files/32388174/README.md)
# Three Body Problem Simulation

A simulation of three bodies moving under each other's gravity, written in JavaScript so it runs in a browser with nothing to install. The whole thing is one HTML file.

The three body problem is the question of predicting how three masses move when each one is pulling on the other two. With two bodies there's a neat formula for the orbit; with three there isn't one in general, and the motion turns out to be chaotic — tiny differences in where things start lead to completely different outcomes. I wanted to see that happen rather than just read about it.

## Running it

Download `index.html` and double click it. That's it, it opens in any browser and starts running.

If the repository has GitHub Pages turned on, there's also a live version at `https://<username>.github.io/<repo-name>/`.

## What you can do

- Watch the three starting bodies orbit each other.
- Click anywhere on the canvas to add another body (up to 8) — useful for seeing how much one extra mass changes everything.
- Press **Restart** to put the three original bodies back.

## How it works

Every frame, for each body, I add up the gravitational pull from every other body using Newton's law of gravitation:

```
F = G * m1 * m2 / r^2
```

That gives the size of the force but not its direction, so I use `atan2` to find the angle between the two bodies and resolve the force into horizontal and vertical components with `cos` and `sin` — the same way you resolve forces at an angle in mechanics.

Once I have the total force on a body, Newton's second law gives its acceleration:

```
a = F / m
```

Then I update the velocity with the acceleration and the position with the velocity, one step at a time.

One detail I had to think about: all the forces are worked out *before* anything moves. If you moved each body as soon as you'd calculated its force, the later bodies would be reacting to the new positions of the earlier ones while the earlier ones had reacted to old positions, which isn't right. Doing it in two passes means every body sees the same snapshot of the system.

The trail behind each body is just a list of its last 300 positions, drawn as small dots, with the oldest removed each frame.

There's also a minimum distance cap. Because the force depends on `1/r^2`, two bodies passing very close would produce an enormous force and fling each other off the screen instantly, which is a numerical artefact rather than real physics. Capping the distance at 20 pixels keeps it sensible.

## Why it sometimes flies apart

If you leave it running, often two bodies will swing close together and the third gets thrown off the screen entirely. That isn't a bug — it's the actual behaviour of the real three body problem. A close encounter acts like a gravitational slingshot, giving energy to one body and ejecting it while the other two end up more tightly bound. This genuinely happens to real three-body systems in space.

## Known limitations

These are things I know are wrong or simplified, and would want to fix if I took it further:

- **The integration method is very basic.** I'm using simple (Euler) stepping — update velocity, then position. It's easy to follow but not accurate, and errors build up over time, so energy isn't properly conserved and orbits slowly drift. A better method like Runge-Kutta or Verlet would hold an orbit together far longer.
- **It's frame-rate dependent.** Each frame counts as one time step instead of using a real elapsed time, so it runs slightly differently on a 60Hz and a 120Hz screen. Multiplying by a proper time step would fix this.
- **The units aren't real.** `G = 2` and the starting velocities were tuned by trial and error until it looked good on screen. Nothing is to scale with actual masses or distances.
- **No collisions.** Bodies pass straight through each other; the minimum distance cap just stops the maths exploding.
- **It's 2D.** Real orbital mechanics is three-dimensional.

## Things I'd like to add

- A better integrator, plus a readout of total energy so you can actually see how much the current method drifts.
- Sliders for mass and the gravitational constant instead of editing the code.
- A fourth, very light test particle dropped near the Lagrange points of a heavy pair, to see which of those points hold onto it and which don't.

## Credit

I started from a Python/pygame n-body example I found online, which had the bodies bouncing off the edges of the screen. I rewrote it in JavaScript so it would run in a browser, and took the walls out, since there aren't any walls in space and bouncing isn't part of the three body problem.
