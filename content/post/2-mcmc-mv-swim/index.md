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

## Recapitulation
In August 2022, I published a lengthy blog post describing a [very nerdy plan to swim across Vineyard Sound](../1-adcirc-mv-swim/).  As the crow flies, Martha’s Vineyard and Woods Hole are separated by only 3.3 miles; however, the sea separating these two landmasses is infamously tempestuous, with tidally-driven currents routinely exceeding 5 knots.  My goal was to leverage NOAA's [ADvanced CIRCulation (ADCIRC) model](https://adcirc.org/) to predict these currents, and determine a sensible combination of swim parameters -- departure time, speed, and heading -- that yielded reasonable trajectories across Vineyard Sound.

{{< figure src="mv_tides.gif" id="mv_tides" numbered=true >}}

Specifically, I looked for solutions that were:
- **Safe.**  The swim route must avoid ferry traffic.
- **Timely.**  The swim must not exceed 2.5 hours, to avoid the strongest currents.
- **Practical.**  The swimmer must arrive near the intended destination.
- **Efficient.**  The swimmer should not fight the current, if possible.

My simple guess-and-check approach worked well enough -- the swim was a success, and we arrived safely on shore near our intended destination, a little less than two hours after we departed.  However, some inquisitive minds soon began to wonder: *"Could it have been done better?"*

{{< figure src="mv_swim_finish.jpg" id="mv_swim_finish" caption="Swimmers arriving on the shore of Martha's Vineyard, near Lake Tashmoo." numbered=true >}}

## Casey's Need for Speed
In the days and weeks after I published the original [post](https://www.blakecole.com/post/1-adcirc-mv-swim/), I received a great deal of positive feedback on my work, and was admittedly somewhat pleased with myself.  I had used my brain to plan a logistically complex adventure which my body executed, and that felt very good.  I was still indulgently basking in self-satisfaction when I received a message from my good friend and colleague, [Casey Handmer](https://www.caseyhandmer.com/home), which said something along the lines of,
>You should optimize the swim.

I swallowed hard.  I knew he was right, but I didn't want to admit it.  Admitting it would mean more work for me, and not just a little more -- I suspected I would need to familiarize myself with some super fancy gradient-free, nonlinear, nonconvex optimization routine.  At the time, I was already five years into my PhD program, and could feel the institutional pressure mounting: I needed to defend my thesis soon, or risk being kicked out of the program.  I bristled at Casey's nonchalance, which seemed to suggest that this problem -- which I didn't even know how to structure -- could be solved easily in an afternoon.  Suppressing my indignation, I informed Casey that I wasn't going to optimize the swim, and that I needed to focus on my doctoral research.  He replied quickly, and asked me to send him the tidal current simulation data so that he could do it himself.

This is classic Casey Handmer.  For those of you who do not know him, that sucks -- he is certainly one of the most fascinating people I have ever met.  He seems to crave technical problems like most people crave sweets or other sensory indulgences.  A physicist named Stanislaw Ulam (more on him later) once remarked, "the mathematicians know a great deal about very little, and the physicists [know] very little about a great deal."  Casey knows basically everything about everything.   At the age of 19, he traveled to Vietnam by himself, and hitchhiked through China and Russia before arriving in Italy; he speaks multiple languages, sings, and plays the trombone; when he isn't riding his unicycle, he likes to fly airplanes or climb mountains; he has a PhD in theoretical physics from Caltech, and founded [Terraform Industries](https://www.terraformindustries.com/), a company which aims to mitigate climate change by [synthesizing natural gas from sunlight and air](https://terraformindustries.wordpress.com/2022/07/24/terraform-industries-whitepaper/).  He is a caring husband and father of three, and yet still somehow finds time to crank out wide-ranging technical [articles](https://caseyhandmer.wordpress.com/) on a weekly basis.  He is also Australian.  Bizarre.

{{< figure src="casey_handmer.jpg" id="handmer" caption="Casey Handmer." numbered=true >}}

The rate at which Casey solves problems is astounding.  This ability is rooted in an inclination to quickly find first-order solutions first, and then decide whether higher fidelity approaches are warranted.  All good engineers seem to operate this way.  Not me.  I frequently succumb to the siren song of complexity, and spend hours deliberating which state-of-the-art technique I'm going to use to solve a problem.  This an ill-advised strategy: you could spend half a lifetime figuring out the best approach to a problem, and never get around to actually solving it.  Further, you simply cannot know the best approach to a problem without having already solved it a few times (or talked to someone who has), so you might as well start with something simple -- dare I say, something... *random*.  Such was Casey's reasoning when he decided to utilize a relatively simple stochastic methodology to determine the best possible swim route from Woods Hole to Martha's Vineyard.

## Markov Chain Monte Carlo
There is power in randomness -- this is the fundamental premise of so-called *Monte Carlo* methods.  How can this be?  Randomness is inherently chaotic, and chaos is bad, right?  Isn't the foremost goal of science and engineering to distill order from chaos?

Sort of.  Another way to think about randomness is as means of observing reality in an unbiased manner.  Imagine being a journalist who is told to write an article about one of ten equally important subjects -- how should they choose which one to cover?  It obviously wouldn't be fair if they simply picked their favorite topic, so instead, they might randomly select one of the subjects.  By doing so, they ensure that each subject is treated with equal importance, free from any personal bias.  This randomness introduces fairness into the process, enabling a more objective approach to gathering information.  In much the same way, Monte Carlo methods use randomness to explore the possible solutions to a problem, systematically sampling different outcomes to estimate the most likely result (or discover the best solution).  While it may seem counterintuitive, this stochastic process can be surprisingly powerful, as it allows us to explore a broad range of scenarios and capture complex behaviors that deterministic methods might miss.


### History
Monte Carlo methods trace their origins to the 1940s, when they were developed as part of the Manhattan Project to aid in the [design of nuclear weapons](https://www.lanl.gov/media/publications/actinide-research-quarterly/1123-hitting-the-jackpot-the-birth-of-the-monte-carlo-method).  Pioneered by scientists such as [Stanislaw Ulam](https://en.wikipedia.org/wiki/Stanis%C5%82aw_Ulam) and [John von Neumann](https://en.wikipedia.org/wiki/John_von_Neumann), these methods were used to model complex physical processes, including neutron diffusion in fission reactions.  The fundamental idea was to use random sampling to approximate solutions to otherwise intractable mathematical problems, leveraging the power of early computers to perform large-scale simulations.  The name *Monte Carlo* was inspired by the famous casino in Monaco, reflecting the role of chance in the method.

{{< figure src="mc_pi_estimation.png" id="mc_pi" caption="A cannoncial demonstration of the Monte Carlo approach involves the estimation of $\pi$ without geometrical relationships.  As points are randomly scattered inside a unit square, some fall within the inscribed unit circle, and some do not; the fraction of points that land inside the circle approaches $\pi/4$ as the number of points approaches infinity [source: [Iván Mauricio Cely Toro](https://mauriciocely.github.io/blog/2020/08/05/estimating-pi-using-the-monte-carlo-method/)]." numbered=true >}}

A major advance came in the 1950s with the introduction of the Metropolis algorithm by Nicholas Metropolis and his colleagues Marshall Rosenbluth, Arianna Rosenbluth, Augusta Teller, and Edward Teller at Los Alamos National Laboratory.  This method combined Monte Carlo techniques with the mathematics of Markov Chains -- theoretical frameworks describing sequences of possible events in which the probability of each event depends only on the state attained in the previous event, not the full history.  This *memoryless* property makes Markov Chains especially powerful for exploring complex parameter spaces in a controlled (yet random) fashion.  In the Metropolis algorithm, a Markov Chain is constructed in such a way that, over time, it produces samples from a desired probability distribution -- or, equivalently, yields a set of parameters that minimizes a cost function (more on this parallel in a moment).

Why would anyone want to do this?  Well, in short, there quite a few ways in which nuclear fusion does not happen, and Metropolis wanted very badly for it to happen.  I would say more, but that's literally all I know, and even if I did know more, I probably wouldn't blast it out on the internet.  

{{< figure src="markov_chain.png" id="markov_chain" width="200" caption="A diagram representing a two-state Markov process. The numbers are the probability of changing from one state to another state [source: [Wikipedia Commons](https://en.wikipedia.org/wiki/Markov_chain)]." numbered=true >}}

The Metropolis algorithm was later generalized by W.K. Hastings in 1970, resulting in the [Metropolis-Hastings algorithm](https://en.wikipedia.org/wiki/Metropolis%E2%80%93Hastings_algorithm), a flexible and widely-used approach that underpins modern Markov Chain Monte Carlo (MCMC) techniques.  By using carefully designed Markov Chains to guide random sampling, MCMC methods are able to efficiently explore high-dimensional spaces, even when direct sampling is computationally infeasible.

Reversible Jump MCMC (RJ-MCMC) is an extension of traditional MCMC methods, developed by Peter J. Green in the mid-1990s.  Standard MCMC methods like Metropolis–Hastings typically operate in parameter spaces with a fixed dimension -- that is, the number of parameters doesn't change during the optimization.  In contrast, RJ-MCMC introduces special moves that allow the dimension of the parameter space itself to change, dynamically adding or removing parameters ("jumping" between models with different complexities).  This flexibility makes RJ-MCMC ideal for problems where the best number of parameters (like the optimal number of discrete swim headings) isn't known ahead of time.


### Algorithm
If you haven't taken a probability course in a while (or even if you have), you might get bogged down in the mathematical language used to describe the [Metropolis-Hastings algorithm](https://en.wikipedia.org/wiki/Metropolis%E2%80%93Hastings_algorithm).  If you paruse its Wikipedia page, you will find gorgeously obscure statements like:

>The Metropolis–Hastings algorithm can draw samples from any probability distribution with probability density $P(x)$, provided that we know a function $f(x)$ proportional to the density $P$ and the values of $f(x)$ can be calculated.

What?

How does that relate to optimization?

I wondered the same thing, at first.  Do not dismay.

The Metropolis–Hastings algorithm was originally developed to approximate probability distributions by generating a sequence of samples such that, after many iterations, the frequency of samples at any point, $x$, approximates the probability density, $P(x)$.

Very clever people eventually figured out that this same method could be used to minimize a cost function by defining a target probability distribution that preferentially selects good solutions, and rejects bad ones.  Suppose you have a cost function, $C(x),$ that you want to minimize.  You could then define a target probability distribution, $P(x)$, where solutions with lower costs have higher probabilities:

$$
P(x) \propto e^{\beta C(x)},
$$
where $\beta$ is a positive parameter controlling the sharpness of the distribution.  If you run the Metropolis–Hastings algorithm with this distribution, the generated "samples" -- which represent sets of parameters, $x$, in this instance -- will preferentially come from low-cost regions of the parameter space, because these samples have a higher probability of being selected.

If none of *that* made sense to you, I am sorry; but, don't give up yet, because I have great news: the Metropolis–Hastings algorithm is shockingly easy to implement, even if you don't fully understand the underlying theory.  This means you (yes *you*!) can optimize all sorts of cool things in your life.

These are the key steps:
{{< callout block "Metropolis-Hastings in Plain English" >}}
1. Define a cost function to evaluate the quality of a prospective solution.
2. Propose an initial solution, and compute its cost.
3. Introduce a random perturbation to the previous solution, and compute the new cost.
   - If the perturbation results in a **lower** cost, accept the solution.
   - If the perturbation results in a **higher** cost, accept the solution with probability $\alpha$, where $\alpha$ is an *acceptance ratio* that depends on the difference between the previous cost and the current cost.  The acceptance ratio can be systematically adjusted over time (this is called *simulated annealing*).
   - If the perturbation results in the **lowest** cost thus far, store the solution.
5. Repeat **Step 3** until a convergence criterion (or iteration limit) is met.
{{< /callout >}}

Or, if you're more of a flowchart kind of person:
{{< mermaid >}}
flowchart TD
    A[Initial Guess] --> B[Compute Cost]
    B --> C[Modify Solution]
    C --> D[Compute Cost]
    D -- $C_k < C_{k-1}$ --> E[Accept New Solution]
    D -- $C_k \geq C_{k-1}$ --> F{Roll Dice}
    F -- $\mathcal{U}(0,1) < \alpha_k$ --> E
    F -- $\mathcal{U}(0,1) \geq \alpha_k$ --> G[Keep Old Solution]
    G --> H[Current Solution]
    E --> H[Current Solution]
    H --> C
{{< /mermaid >}}

Why periodically accept worse (higher cost) solutions?

Imagine you're hiking in the mountains on a foggy day, searching for the highest peak.  If you simply climb upwards at every step, you might find yourself atop a small hill and assume you've reached the highest point (these little hilltops are called a *local maxima* or *local extrema* in nerd speak... we despise them).  In order to locate the highest peak -- the *global maximum* (which we love) -- you might need to occasionally descend to lower elevations, even if it seems counterproductive in the moment.

{{< figure src="hiker.png" id="hiker" caption="Sometimes you have to go lower to get higher [source: DALL-E 16.20.12]." numbered=true >}}

To me, the fact that this algorithm actually works (most of the time) is nothing short of astounding -- it is basically "guess-and-check" on steroids.  And yet, despite its simplicity, this technique has revolutionized the fields of nuclear physics, economics, genetics, and climate science, just to name a few.  It also paved the way for advances in [Bayesian statistics](https://en.wikipedia.org/wiki/Bayesian_statistics) and modern machine learning techniques.  The power of this approach cannot be attributed to luck or magic; it is underwritten by *randomness*, and the properties inherent thereto.

### Python Demo
I will provide a very basic 1-D implementation of the Metropolis–Hastings algorithm here; it is functional (and hopefully illustrative), but not particularly interesting.  Part of what makes the MCMC approach so fascinating is its generality: it can explore parameter spaces of varying dimension, and the cost function need not be differentiable or continuous.  For those interested in diving into the weeds, I would highly recommend checking out my full [MCMC swim route optimization](https://github.com/blakecole/mv-mcmc) repository on Github.  It has some really useful functions and a few clever flourishes, some of which will be detailed momentarily.  Feel free to [reach out](../../#contact) if there is anything you find confusing.

```python {style=emacs}
import random
import math

def metropolis_hastings(cost_func, initial, iterations, proposal_std):
    """
    Metropolis-Hastings algorithm for cost function minimization.

    Args:
        cost_func: The cost function to minimize. It should take a single parameter.
        initial: Initial value for the parameter.
        iterations: Number of iterations to run.
        proposal_std: Standard deviation of the Gaussian proposal distribution.

    Returns:
        A tuple (best, best_cost, samples) where:
          - best is the best found parameter value.
          - best_cost is the corresponding cost.
          - samples is a list of the parameter values over the iterations.
    """
    current = initial
    current_cost = cost_func(current)
    best = current
    best_cost = current_cost
    samples = [current]

    for i in range(iterations):
        # Propose a new candidate using a Gaussian random step.
        candidate = current + random.gauss(0, proposal_std)
        candidate_cost = cost_func(candidate)
        
        # Calculate acceptance probability.
        # For minimization, lower cost is better.
        # If the candidate cost is lower, accept it outright.
        # Otherwise, accept it with probability exp(-(candidate_cost - current_cost)).
        if candidate_cost < current_cost:
            accept = True
        else:
            accept_prob = math.exp(-(candidate_cost - current_cost))
            accept = random.random() < accept_prob
        
        if accept:
            current = candidate
            current_cost = candidate_cost
            # Update best if the new candidate is better.
            if candidate_cost < best_cost:
                best = candidate
                best_cost = candidate_cost
        
        samples.append(current)

    return best, best_cost, samples

# Example usage
if __name__ == '__main__':
    # Define a simple cost function, e.g., a parabola with a minimum at x = 2.
    def cost(x):
        return (x - 2) ** 2

    # Run the Metropolis-Hastings algorithm.
    best, best_cost, samples = metropolis_hastings(cost_func=cost,
                                                     initial=0,       # Starting value
                                                     iterations=10000, # Number of iterations
                                                     proposal_std=1.0) # Step size

    print("Best x found:", best)
    print("Cost at best x:", best_cost)

```

## Problem Setup
### Goal
Depart Nobska Beach at any time before 11:00a on July 30th, 2022 and cross Vineyard Sound as fast as possible.

### Constraints
**None**.  We are prioritizing *speed* above pragmatism, above safety, above everything.  Ferries, ruffled feathers, and private beaches be damned (*especially* the private beaches, those can go straight to hell).  Any sequence of headings that delivers the swimmer to the shore of Martha's Vineyard is on the table.

### Assumptions
1. We assume the swimmer is able to maintain a **constant pace** of $1$:$40$/$100yd$.  This is a reasonable pace for an average open water swimmer in good shape.
2. We assume the swimmer departs from a **fixed location** on Nobska Beach, MA, sometime between 1:00a and 11:00a on July 20th, 2022.

### Parameters
We must determine a set of parameters that enables the fastest possible crossing:
1. **Heading Sequence**:  Each heading is pursued for a fixed time interval, $\Delta t;$ the total number of heading changes is not known in advance.
2.  **Start Time**:  This parameter determines the state of the tidal current vector field, and thus exerts a strong influence on the crossing time and optimal heading sequence.

### Methodology
1. Define **swim constants**:
   - Departure location
   - Start time
   - Time step
   
2. Define **optimization constants**:
   - Initial annealing temperature
   - Initial Gaussian bump amplitude limit
   - Number of iterations
   
3. Determine a reasonable **initial guess**.
   1. Simulate a series of *fixed-heading* swims.
   2. Store the solutions which produce valid trajectories (i.e., those that reaches Martha's Vineyard in under 3 hours).
   3. Pick the fastest valid constant-heading solution as the initial guess.
   4. Compute the cost (swim duration) of the initial guess.

4. Randomly choose whether to modify, grow, or shrink the heading vector, according to the following logic:
   ```python {style=emacs}
   pb = 0.33
   pd = 0.33
   pm = 0.34
   move_type = np.random.choice(['birth', 'death', 'modify'], p=[pb, pd, pm])
   
   if move_type == 'modify' and len(new_h) > 0:
       perturbation = generate_bump(new_h, bump_size)
       new_h += perturbation

   elif move_type == 'birth':
       new_heading = np.random.normal(birth_bearing, 20.0)
       insert_idx = len(new_h)
       new_h = np.insert(new_h, insert_idx, new_heading)
    
    elif move_type == 'death' and len(new_h) > 1:
        idx = len(new_h)-1
        removed_heading = new_h[idx]
        new_h = np.delete(new_h, idx)
   ```
   The `generate_bump` function is detailed in the Flourishes section (note: it is very important!)  The `bump_size` parameter is typically set between 5 and 15 degrees at the outset, depending on the user's confidence in their first guess, and reduced $0.1\%$ after every iteration; this allows the optimization routine to conduct a thorough exploration of the parameter space early on, and then refine the solution using smaller perturbations.  The use of a larger initial `bump_size` results in very robust performance, but can sometimes yield sub-optimal solutions; conversely, the use of a smaller initial `bump_size` works better if the initial guess is already pretty good, such that the odds of getting trapped in a local minimum are small.

5. Compute the cost of the candidate solution.

6. Compute the acceptance probability: $\alpha_k = \exp{\left( \frac{\Delta_k}{T_k} \right)}$, where $\Delta_k$ is the cost difference between the candidate and previous solutions, and $T_k$ is the annealing temperature parameter.  We reduce $T_k$ gradually over time using a linear cooling schedule to discourage premature convergence.

7. Keep the candidate solution if both of the following conditions are met:
   1. ***EITHER*** the candidate cost is lower than the previous cost, $C_k < C_{k-1}$, ***OR*** the acceptance probability, $\alpha_k$, is greater than a random number drawn from a uniform distribution, $\mathcal{U}(0,1)$.
   2. The swim trajectory terminates inside the boundary of Martha's Vineyard.

8. If the candidate solution is the lowest-cost solution yet, save it.

9. Return to **Step 4** and repeat until the iteration limit has been reached.


### Useful Functions
1. **Flowfield Interpolation**:  Extract a tidal current vector at any time and position within the discretized computational domain, using the `NearestNDInterpolator` function from the `scipy.interpolate` library.  I also experimented with the `LinearNDInterpolator` function, but the minor improvement in fidelity did not justify the enormous increase in computational cost.

2. **Dead Reckon**: Given a starting point (lon, lat), initial bearing (in degrees), and a distance (in meters), compute the destination point along a *great-circle path* using a modified form of the [Haversine formula](https://en.wikipedia.org/wiki/Haversine_formula) (note: if you like geodesy, check out my [proof of this relationship](../../publication/jrnl-aor-2022/)).  This function permits the simulation of swim trajectories knowing only a set of sequential headings, and the swimmer's speed through water.

3. **Arrival Detection**: Given a point (lon, lat) and a list of points describing a closed region, determine whether the point is inside the closed region, using the [ray-casting algorithm](https://en.wikipedia.org/wiki/Point_in_polygon#Ray_casting_algorithm).  This function is used to determine whether a given sequence of headings produces a valid swim trajectory that reaches Martha's Vineyard.

4. **Closest Approach** (Optional): Compute the closest distance (in meters), and the bearing (in degrees), from a geodetic point (lon, lat) to a closed polygon defined as a (Nx2) NumPy ndarray of (lon, lat) points, using vectorized geodetic functions.  Although this function is not strictly necessary, it was useful when I was debugging my optimization routine.




## Results
Before I present my own solutions, I'd like to show Casey's work.  His results aren't quite as polished, but he probably cranked them out in about the same time as I spent formatting one of my plots (Fuck it.  Data visualization is important.  Beauty is important.  Making [good plots](https://www.datawrapper.de/blog/beautifulcolors#1) is worth the time and effort.  I will die on this hill).  Further, Casey's findings were compelling enough to inspire me to write my own MCMC code, and publish this blog.  Thanks, Cazza!

### Casey's Solution
Shortly after I sent Casey the tidal simulation data, I received the following email:

<div style="background-color: #00131A; color: #F2F2F2; padding: 10px;">
Hi Blake, 

Finally getting around to shooting you this info.

I relaxed the constraint that the headings be constant to see if it was possible to find a faster routing. I coded a function that would (quickly) attempt to find an optimal sequence of headings as a function of 
- departure location and time
- arrival location
- minimizing time in the ferry zone
- pace together with an interpolated background dataset of known (simulated) current flows.

This graph shows an example output, with headings in degrees as a function of time (units in multiples of 3 minutes). For a particular combination of start time and pace, close to 140 degrees the whole way is adequate, as shown with the orange line. Leaving earlier or later, favors different headings to take advantage of the water speed.

{{< figure src="casey_heading_vs_time.png" id="casey_soln_1" width="600" caption="<center>Optimal swim heading sequences for assorted start times. [source: Casey Handmer]</center>" numbered=true >}}

This ground track shows, as a function of latitude and longitude in degrees, the ground tracks corresponding to the curves above. The bright green and red are baselines premised on 140 degree headings the whole time, while the colored red and green dots demarcate the boundaries of the ferry zone. Blue dots show the grid on which tide current values are known with certainty, and hence an inference of the shoreline location. Not every start time converged to the desired landing point but most of them did.

{{< figure src="casey_trajectories.png" id="casey_soln_2" width="600" caption="<center>Optimal swim trajectories for assorted start times. [source: Casey Handmer]</center>" numbered=true >}}

The time saved compared to 140 degrees baseline varied between 5 and 20 minutes, some of which could be attributed to reducing the overall distance needed to be covered. Fortunately Martha's Vineyard has a big beachy coast which is hard to miss!

Compared to Blake and friends' actual swim, the solutions are about 20-30 minutes faster. I think this is probably because the simulated swimmers routed close to the shore on departure, waiting until they were in strong current before zooming across the ferry zone. Blake's strategy to swim directly across the zone in still water meant their group took longer to get into the faster water.

This optimization effort was very hacky, and I'm sure there's another 3-5 minutes of performance to be squeezed out with concerted effort.

Casey
</div>


### My Solution


{{< gallery album="mv-mcmc-solutions" >}}

Early on, I was using a cost function that combined `swim_time` and `distance_from_shore` as a weighted sum, hoping this would permit sufficient solution flexibility (i.e. the simulated swimmer can make landfall at any point along the shoreline), while guiding the optimization routine toward solutions that were both fast *and* valid.  However, I found that the routine frequently opted for solutions that never made it to shore -- it was prioritizing a smaller value of `swim_time` over the necessary condition that `distance_from_shore = 0'.  I initially rectified this solution by tuning the cost function weights, but I hated this solution; it felt flimsy.  So, I eventually figured out a way to reject invalid solutions, and remove `distance_from_shore` from the cost function.


### Your Turn!
It is often said (by lunatics, mostly) that there are [many ways to skin a cat](https://en.wiktionary.org/wiki/there%27s_more_than_one_way_to_skin_a_cat); likewise, I am certain that there are other, perhaps better, ways to approach this problem.  But, again, there are so many cats to skin, and so little time.  Please [reach out](../../#contact) if you think you have stumbled upon a more effective approach (i.e., faster convergence, more robust, better overall solution) -- I'd love to chat.

## Flourishes

### Gaussian Bump
A common and straightforward method for proposing new candidate solutions in MCMC optimization involves directly perturbing the parameter vector with random noise.  For instance, one typically selects a random index from the parameter vector and adds a small perturbation drawn from a Gaussian distribution with zero mean and a chosen standard deviation.  A Python implementation of this approach might look like this:

```python {style=emacs}
proposal_std = 1.0
idx = np.random.randint(len(old_soln))
perturbation = np.random.normal(0, proposal_std)
new_soln[idx] = old_soln + perturbation
```

Although easy to implement, this method tends to produce isolated, incremental changes, potentially limiting the ability of the algorithm to explore broader variations in the solution space efficiently.  We can often leverage domain-specific expertise to modify the *proposal step*, and drastically improve both the convergence rate and quality of the resulting solutions.  For instance, when optimizing a swim route, we can be fairly confident that swimmer heading will not change abruptly, and can modify our proposals accordingly.

Rather than simply adding random noise to the swimmer heading vector, we can preserve smoothness by adding a *Gaussian bump*.  The Gaussian bump is characterized by three randomly selected parameters: the **center** (index in the heading vector), the **width** (extent of the perturbation along the heading vector), and the **amplitude** (magnitude and direction of the perturbation).

{{< figure src="gaussian_bumps_invert.png" id="bumps" caption="An set of Gaussian functions with varying center (i.e. mean), width (i.e. variance), and amplitude [source: [Wikimedia Commons](https://en.wikipedia.org/wiki/File:Normal_Distribution_PDF.svg)]." numbered=true >}}

By adjusting these parameters randomly within carefully chosen ranges, the function introduces controlled yet diverse variability, allowing the optimization process to effectively explore different route possibilities without creating unrealistic or overly abrupt path changes.  This little flourish was Casey's idea, and literally made the optimization work.  A Python implementation of the Gaussian bump might look like this:

```python {style=emacs}
def generate_bump(headings, bump_size=1.0):
    # Random center in [-0.2, 1.2]
    center = np.random.uniform(-0.2, 1.2)
    
    # Random width: generate log-width uniformly between log(0.05) and log(1), then exponentiate
    log_width = np.random.uniform(np.log(0.05), np.log(1.0))
    width = np.exp(log_width)
    
    # Random amplitude in [-bump_size, bump_size]
    amplitude = np.random.uniform(-bump_size, bump_size)
    
    # Create sample points: the number of points equals the length of headings
    n_points = len(headings)
    x_vals = np.linspace(0, 1, n_points)
    
    # Evaluate the Gaussian bump at each sample point
    bump = amplitude * np.exp(-((x_vals - center) ** 2) / (width ** 2))
    
    return bump
```

To enhance convergence toward an optimal solution, the amplitude range of these perturbations -- controlled by the parameter `bump_size` -- is reduced after each iteration (e.g., using `bump_size *= 0.999` in the optimization loop).  Initially, larger perturbations encourage exploration across a broad set of candidate solutions, preventing premature convergence on local minima. As the optimization progresses, decreasing `bump_size` narrows the search area, transitioning from broad exploration toward refined, localized adjustments.  This adaptive strategy balances efficient global exploration with precise local optimization, resulting in a systematically improved swim trajectory.

I was honestly shocked at how well this worked.  Initially, I was using a fixed value of `bump_size = 3.0` (units: degrees), and found that the final solution was highly dependent on the initial guess.  The Gaussian bump technique, with exponential cooling, yeilded a much more robust solver capable of converging to the global optimum regardless of the initial guess.


### Simulated Annealing
Generally speaking, simulated annealing is a technique used to gradually reduce the rate of change experienced by a system over time; indeed, its name was inspired by the physical process of slowly cooling molten metal to achieve a stable, low-energy crystal structure.  In the context of optimization, it involves slowly decreasing a *temperature parameter* to control the acceptance probability of proposed solutions.  In the Metropolis-Hastings algorithm, the acceptance probability is typically computed in the following way:

$$
\alpha_k = \exp{\left( \frac{\Delta_k}{T_k} \right)}
$$

where $T_k$ is the temperature parameter, and $\Delta_k = C_k - C_{k-1}$ is the difference between the old and new solution costs. For simple problems (as in the Python Demo provided above), it is often sufficient to simply set $T=1$; however, by allowing the temperature parameter to gradually decrease over time, we can achieve both broad *exploration* early in the optimization process, and focused *exploitation* and refinement as convergence progresses.

The temperature parameter is ofted reduced according to a predefined *cooling schedule*, a few of which will be outlined here:

#### Exponential
This is likely the most widely-used cooling method, due to its simplicity and effectiveness:

$$
T_k = \beta T_{k-1}
$$
where $T_k$ is the temperature at iteration $k$, and $\beta$ is a constant slightly less than $1$ (typically $0.80 \leq \beta \leq 0.99$), controlling how rapidly temperature decreases.  However, it should be noted that exponential decay reduces the temperature proportionally each step, leading to a rapid reduction in temperature early on, quickly suppressing exploration.  I found that this cooling technique resulted in an overly-aggressive reduction of the acceptance probability (even when I set $\beta$ very close to $1$ ($\beta = 0.999$); however, it was perfectly suited to reduce the `bump_size` parameter over time.

#### Linear
This is another straightforward method, useful when the number of iterations $N$ is known in advance.  Linear cooling reduces temperature at a constant rate rather than proportionally:

$$
T_k = T_{k-1} - \Delta T, \qquad \Delta T = \frac{T_0 - T_{final}}{N}
$$
This method is simpler to implement, but less flexible since it requires knowing the final iteration count in advance.  It also results in much slower cooling, which was more appropriate for my application, and resulted in a more thorough, robust exploration of the solution space.



A figure:
{{< figure src="featured.png" id="demo" caption="An image. [Photo: Credits]" numbered=true >}}

