---
theme: default
class: text-center justify-center justify-center m-auto
highlighter: shiki
transition: slide-up
title: Pyrknot Euler Animation
---

# Pyrknot 

Pyrknot is modeled as a perfect sphere. Seven heavenly bodies—six co-planets and the sun—each project a **great circle** onto its surface. This circle acts as the absolute dividing line between where a body is visible and where it is not.
<ThreeScene type="one" style="transform: scale(1.2) translateY(-30px);" />


---
transition: slide-left
class: py-20 grid-cols-2 text-sm px-20
---

# Problem Statement

From the question we know that there exist six "coplanets" such that each planet is in independently uniform positions in the sky. The last celestial body (the sun) is not visible.

$$
\begin{gathered}
    \text{"Probability of Seeing All Planets" } S_c := \Pi_{i=1}^{6} P(c_i) \quad
    \text{"Probability of Seeing the Sun" } S_s := P(s) \\
    P(parade) :=  S_c \times S_s
\end{gathered}
$$ 


We can define a "parade" as when we are given sight to all six coplanets while the sun is not visible (since it's night).

- **Probability of Seeing All Planets:**  
  $S_c := \prod_{i=1}^{6} P(c_i)$

- **Probability of Seeing the Sun:**  
  $S_s := P(s)$

- **Probability of Parade:**  
  $P(\text{parade}) := S_c \times S_s$

---
transition: slide-up
class:  px-50
---

## Targets

In the context of the Pyrknot planetary puzzle we have been working through, $\alpha$ and $\beta$ are the exact numerical values that define your friend's chances of seeing the planetary parade.

This is the basic chance of seeing all 6 planets lined up in the sky while standing on the ground. The planet's surface is divided into 44 equal regions by the movement of the 7 celestial bodies. Only one region gives a perfect view of the parade. So, your chance of being in the right spot is exactly:

$$
\alpha \equiv P(parade)_R
$$

The growth factor shows how much your chances improve by building the tower. When your friend uses the tower, their view expands, increasing their odds of seeing the parade. But if part of the new view is in daylight, that extra area doesn't help. So, only the nighttime area adds to the improved chance.

$$
    \beta \equiv P(parade)_{r}
$$


---
transition: slide-up
class: text-center
---

# Lorem ipsum

<video controls autoplay muted loop class="mx-auto h-80 rounded-3xl shadow-lg">
    <source src="/home/sybil/Downloads/pres/pyrknot-presentation/src/SkyVisibility.mp4" type="video/mp4">
    Your browser does not support the video tag.
</video>


---
transition: slide-up
class: text-center
---

## Mapping Intersections
<br> <br> 
How do these boundaries intersect? Any two great circles cross at *exactly* two antipodal points. With seven circles, we have $C(7,2) = 21$ pairs. This gives us a total of **$V = 42$** vertices.
<br> <br> 
<div class="grid grid-cols-2 mt-8">
    <Diagram2 />
    <ThreeScene type="two" style="transform: scale(1) translateY(-100px);" />
</div>

---
transition: slide-up
class: text-center
---


Every great circle slices the sphere into two equal hemispheres. Stand on one side, and the body is in your sky; cross the line, and it dips below the horizon. Let's map out all seven, one by one.

<video controls autoplay muted loop class="mx-auto h-80 rounded-3xl shadow-lg">
    <source src="/home/sybil/Downloads/pres/pyrknot-presentation/src/PlanetaryParadeGraph.mp4" type="video/mp4">
    Your browser does not support the video tag.
</video>



---
transition: slide-up
class: text-center
---

## Multiple Bisections

Now, focus on a single great circle. The six others cross it twice each, placing 12 points along its circumference. These points divide the circle into 12 distinct arcs, or edges. Across all seven circles, this yields **$E = 84$** total edges.

<div class="grid grid-cols-2 mt-8">
  <Diagram3 /> <ThreeScene type="full" style="transform: scale(1) translateY(-100px);" />
</div>

---
transition: slide-up
class: text
---

## Probability Statement
This results in seven banded regions: one for the sun and six for the planets. Since positioning is uniformly probable, seeing a heavenly body $h_i$ is effectively a coin toss.

The target probability is defined by the chain rule:

$$P(\text{Parade}) = P(\neg s) \times P(c_1 \mid \neg s) \times P(c_2 \mid c_1 \cap \neg s) \times \dots \times P(c_6 \mid \bigcap_{i=1}^{5} c_i \cap \neg s)$$

* **$P(\neg s)$:** Baseline probability of being on the night side ($1/2$).
* **$P(c_1 \mid \neg s)$:** Probability planet 1 is visible, given you are in darkness.
* **$P(c_n \mid \dots)$:** Probability each subsequent planet is visible, given all previous bodies are also visible.


---
transition: slide-up
class: text
---

## Graph Persective

Calculating these geometric probabilities directly is impractical; each added planet creates increasingly complex spherical polygons. Instead, we can map these banded regions to a graph composed of:
* **Vertices ($V$):** Intersection points of the great circles.
* **Edges ($E$):** Segments connecting the vertices.
* **Bands ($B$):** Enclosed surface areas formed by the web.


---
transition: slide-up
class: text-center
---
## Euler's Formula for Spheres

Euler discovered that: no matter how you slice up a sphere with intersecting lines, if you take the number of intersections (V), subtract the number of line segments (E), and add the number of distinct surface regions (B), the answer will always equal 2. Where $B$ represents what we want to find, $\alpha$


<div class="flex justify-center mt-8">
  <Diagram4 />
</div>

---
transition: slide-up
class: text-center
---
## Regions of Interest

The sphere is thus carved into 44 distinct viewing regions. Each band offers a completely unique combination of visible bodies. Crucially, only **one** of these 44 bands provides the perfect view: all six co-planets up, with the sun hidden safely below the horizon.

<ThreeScene type="full" />
Assuming all 44 regions have equal surface area and the co-planets land uniformly at random, the chance of your friend standing in that single lucky region is exactly **$\alpha = 1/44$**.


---
transition: slide-up
class: text-center
---

# The Tower's Effect ($\beta$)

What happens if your friend builds a tower to improve those odds? When your friend builds a tower, they push their horizon outward by a distance $r$.

<ThreeScene type="full" />

---
transition: slide-up
class: text-center
---

# Expanding the Horizon

This expands the borders of their viewing region, increasing their odds of catching the parade. However, there is a catch: pushing the border toward the star's region ruins the view with daylight. 

<ThreeScene type="full" />

---
transition: slide-up
class: text-center
---

# The Growth Factor

The net gain in surface area—and thus the overall increase in probability—scales specifically by $\beta \equiv P(parade)_r$. This represents the growth factor, telling you exactly how much building the tower actually helps.

<ThreeScene type="full" />
