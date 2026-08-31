---
layout: post
title: "1.1: Setup and my first sequential system"
date: 2026-08-25
categories: [ray-optics]
---

**Problem:** Get Zemax OpticStudio Student running and build the simplest possible sequential system, a single plano-convex lens focusing a collimated beam. As a sanity check before anything more complex.

**Approach:**
This is the direct simulation equivalent of focusing an OBIS diode laser onto a sample on the bench: collimated input, one lens, a focal spot. I set the aperture to match a beam diameter I've actually used (10 mm EPD), picked a plano-convex N-BK7 lens, set a single wavelength of 532 nm, and let Zemax trace the marginal and chief rays to the paraxial focus using a Quick Focus solve.

> **A quick definition- F/#:** the F-number (F/#) is just the lens's focal length divided by its clear aperture diameter (F/# = f / D). A "fast" lens (small F/#, like F/1.4) collects a wide cone of light and focuses it steeply, but is harder to correct for aberrations. A "slow" lens (large F/#, like F/22) collects a narrow cone and focuses it gently, which is inherently easier to make sharp but throws away light. It's the same trade-off as opening up or stopping down a camera aperture, wider aperture, more light, more optical error; narrower aperture, less light, cleaner focus. It matters here because I deliberately changed F/# (by changing the aperture, not the lens) to see exactly how much of my focal spot was the lens's fault versus a fundamental limit.

To go a step further, I also stopped the aperture down from 10 mm to 4 mm to see how much of the focal spot size was actually aberration versus diffraction, the kind of check I'd normally only get indirectly, from a beam profiler trace looking "a bit too big."

**Result:**
At 10 mm EPD (F/# ≈ 9), the RMS spot radius was **9.032 µm**, larger than the diffraction-limited Airy radius of ≈ 5.8 µm at that F/#, meaning the system was aberration-limited, not diffraction-limited. Stopping down to 4 mm EPD (F/# ≈ 22.5) dropped the RMS spot radius to **0.575 µm**, well below its corresponding Airy radius (≈ 14.6 µm), now effectively diffraction-limited.

> **What "RMS spot radius" physically means:** Zemax fires a grid of rays through the lens and traces where each one lands on the image plane. Because of aberrations, they don't all land in exactly the same spot, they spread into a small cloud. RMS (root-mean-square) radius is essentially the "average distance" of that cloud from its centroid, weighted so outliers count more (it's the same RMS you'd use for a standard deviation). Physically, it's a proxy for how big and blurry your focused spot actually is: a smaller RMS radius means a tighter, sharper focus. It's not the literal spot diameter you'd see on a screen (real spots have a soft, non-uniform intensity profile), but it's the standard single number optical engineers use to compare two designs' image quality at a glance.

![3D layout of the single-lens system](/assets/week1/layout.png)
*Figure 1: 3D Layout: collimated 532 nm beam entering a plano-convex N-BK7 lens, focusing to a point.*

![Spot diagram at 10mm EPD](/assets/week1/spot_10mm.png)
*Figure 2: Spot diagram at F/# ≈ 9. RMS radius 9.032 µm, visibly larger and less symmetric than the diffraction-limited case below.*

![Spot diagram at 4mm EPD](/assets/week1/spot-4mm.png)
*Figure 3: Spot diagram at F/# ≈ 22.5. RMS radius 0.575 µm, tight and essentially diffraction-limited.*

**What surprised me:**
The ratio of the two spot sizes (9.032 / 0.575 ≈ **15.7×**) matched the textbook cube-law prediction for third-order spherical aberration, (10/4)³ ≈ 15.6×, almost exactly. Seeing that match land so cleanly made the aberration trade-off concrete in a way that just squinting at a focal spot on a bench never quite does. Stopping down clearly fixes the geometric spot, but you're trading it directly for a bigger diffraction-limited floor (worse resolution, less throughput) at the higher F/#.

**Files:** [Week 1 project files on GitHub](https://github.com/spark-violet/zemax-learning-journal/tree/main/01_ray_optics): `week1_single_lens_focus.zmx`

---
*Full lab-notebook-style writeup available on [GitHub](https://github.com/spark-violet/zemax-learning-journal/tree/main/01_ray_optics/experiment-1-single-lens).*
