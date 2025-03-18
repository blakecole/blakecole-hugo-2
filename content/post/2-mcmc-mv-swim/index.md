---
title: "Leveraging Reversible Jump Markov Chain Monte Carlo (RJ-MCMC) Optimization to Plan a (Better) Swim to Martha's Vineyard"
subtitle: ""
summary: "One of my more talented friends helped me determine the *best possible* swim route to Martha's Vineyard.  Bless you, Casey Handmer."
authors: admin
tags: []
categories: []
date: 2025-02-21T00:00:00
lastmod: 2025-03-18T17:45:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "Reversible Jump Markov Chain Monte Carlo is a stochastic, sampling-based optimization method that can explore parameter spaces of varying dimension by introducing or removing parameters while maintaining detailed balance."
  focal_point: Smart
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

## Preamble
### Lesser Stuff
Even lesser stuff

A link:
[Dr. Peter Traykovski](https://www2.whoi.edu/staff/ptraykovski/)

A figure:
{{< figure src="featured.png" id="demo" caption="An image. [Photo: Credits]" numbered=true >}}

Some math:
However, the shallow-water equations require the simultaneous computation of water level *and* flow velocity at each computational grid point.  It is useful to look at a simplified 1D form of the shallow-water equations (sometimes called the *Saint-Venant equations*) to illustrate this point:

$$\frac{\partial A}{\partial t} + \frac{\partial \left(Au \right)}{\partial x} = 0$$

$$\frac{\partial u}{\partial t} + u\frac{\partial u}{\partial x} + g\frac{\partial \zeta}{\partial x} = - \frac{P}{A} \frac{\tau}{\rho}$$

The first of these equations can be recognized as the continuity equation.  Together, these two equations describe one-dimensional flow of an incompressible fluid in an open channel, where $x$ is the spatial coordinate running along the length of the channel; $t$ represents time; $A(x,t)$ is the cross-sectional area of the flow; $\tau(x,t)$ is the shear stress along the wetted perimeter $P(x,t)$; $u(x,t)$ is the **flow velocity** along the channel axis; and $\zeta(x,t)$ is the **free surface elevation**.


Of these three characteristics, *swim duration* is most important.  Assuming the swimmer is providing an approximately constant level of effort, swim duration serves as a good heuristic for *fatigue*.  Conversely, *swim distance* is less informative.  Consider the boundary cases:
- A swimmer opposing a strong current will move nowhere, and nevertheless become completely exhausted after some time.
- A swimmer aided by a strong current could exert no effort, and nevertheless arrive at their destination some arbitrary distance downstream.

For this particular swim, *arrival destination* is also quite important, as it serves as a heuristic for risk of encountering ferry traffic.  Finishing too far to the northeast, near West Chop Point, places one in a strong northeasterly current, directly adjacent to ferry traffic entering and exiting Vineyard Haven Bay.  In this region, a small miscalculation could have catastrophic effects.

One final caveat before I dive into the simulation results:
>**Nothing about what I have done here is "optimal."**

I simply poked around the parameter space (departure time, swimmer speed, and swimmer heading) until I found trajectories that seemed "reasonable."  Specifically, I looked for paths that were:
- **Safe.**  The route must avoid ferry traffic.
- **Efficient.**  The swimmer should not fight the current (mathematically speaking, the dot product of swimmer velocity and current velocity should never be negative).
- **Timely.**  The swim must be completed in less than 2.5 hours to avoid the strongest currents.
- **Practical.**  The swimmer must arrive near the intended destination.

This struck me as a justifiable approach, given that the parameter search space was actually quite small; indeed, Rick had already identified a relatively compact range of feasible departure dates and times.  Further, we already knew where we wanted to start (Nobska Beach), where we wanted to finish (Lake Tashmoo), and how fast we could swim.  The only remaining unknowns were the exact departure time, swim pace, and where we should point our bodies!

Here is a quick summary of the simulations I ran:
- Constant $145^{\circ}$ Heading
- Forced Transit Through Predefined Waypoints
- Hybrid Strategy: Transit 1st Waypoint + Constant $140^{\circ}$ Heading
  - Departure Times
    - 6:00a
    - 6:18a
    - 6:30a
  - Swim Pace
    - 1:40/100yd
    - 2:00/100yd

Finally, here are a few notes on the animations that might be useful:
- The **colormap** corresponds to the magnitude of current velocity.
- The **black arrows** indicate the direction of the current velocity.
- The **black lines** are rhumb lines between predefined waypoints, represented by **black circles**.
- The **dashed magenta line** represents the swimmer trajectory at each time step.
- The **magenta arrow** indicates the swimmer Course Over Ground (COG): this is the net direction of swimmer movement, including current action.
- The **dark purple arrow** indicates swimmer heading: this is the direction in which the swimmer is producing thrust.  Note that when the current goes completely slack, the **magenta arrow** and **dark purple arrow** become perfectly aligned.

A video:
{{< vimeo-enhanced 737940441 >}}
