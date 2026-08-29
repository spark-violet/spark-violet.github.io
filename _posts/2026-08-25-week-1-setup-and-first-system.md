---
layout: post
title: "Week 1: Setup and my first sequential system"
date: 2026-08-25
categories: [ray-optics]
---

**Problem:** Get Zemax OpticStudio Student running and build the simplest possible sequential system, a single plano-convex lens focusing a collimated beam. As a sanity check before anything more complex.

**Approach:**
This is the direct simulation equivalent of focusing an OBIS diode laser onto a sample on the bench: collimated input, one lens, a focal spot. I set the aperture to match a beam diameter I've actually used (10 mm EPD), picked a plano-convex N-BK7 lens, set a single wavelength of 532 nm, and let Zemax trace the marginal and chief rays to the paraxial focus using a Quick Focus solve.

To go a step further, I also stopped the aperture down to 4 mm to see how much of the focal spot size was actually aberration versus diffraction. The kind of check I'd normally only get indirectly, from a beam profiler trace looking "a bit too big."

**Result:**
At 10 mm EPD (F/# ≈ 9), the RMS spot radius was **9.032 µm**, larger than the diffraction-limited Airy radius of ≈ 5.8 µm at that F/#, meaning the system was aberration-limited, not diffraction-limited. Stopping down to 4 mm EPD (F/# ≈ 22.5) dropped the RMS spot radius to **0.575 µm**, well below its corresponding Airy radius (≈ 14.6 µm), now effectively diffraction-limited.

*(Insert here: screenshot of the 3D Layout and both Spot Diagrams.)*

**What surprised me:**
The ratio of the two spot sizes (9.032 / 0.575 ≈ **15.7×**) matched the textbook cube-law prediction for third-order spherical aberration, (10/4)³ ≈ 15.6×, almost exactly. Seeing that match land so cleanly made the aberration trade-off concrete in a way that just squinting at a focal spot on a bench never quite does, stopping down clearly fixes the geometric spot, but you're trading it directly for a bigger diffraction-limited floor (worse resolution, less throughput) at the higher F/#.

**Files:** [Week 1 project files on GitHub]((https://github.com/spark-violet/zemax-learning-journal/tree/main/01_ray_optics) : `week1_single_lens_focus.zmx`

---
*Full lab-notebook-style writeup (Aim, Theory, Method, Results, Observations, Inference, Next Steps) with lens schematic available separately.*
