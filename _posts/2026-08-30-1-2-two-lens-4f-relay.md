---
layout: post
title: "1.2. A 4f two-lens relay"
date: 2026-08-30
categories: [ray-optics, system-design]
---

**Problem:** Extend single lens project into a **4f relay**. This is done using two identical lenses arranged such that a point at the front focal plane of Lens 1 gets re-imaged at the back focal plane of Lens 2, with a shared "Fourier plane" exactly midway between them. This is the same geometry behind Fourier-space and confocal imaging setups I've aligned by hand during my PhD.

**Approach:**
I reused single lens project's exact lens prescription (plano-convex N-BK7, R = 50 mm, EFFL ≈ 96.25 mm) for both elements, placed the object 96 mm in front of Lens 1 (its front focal plane), spaced the two lenses 192 mm apart (2f), and set the final image distance to another 96 mm. I ran two configurations to compare:

- **Config A** - aperture stop left at Lens 1 (the simplest working version)
- **Config B** - aperture stop moved to the Fourier plane, the textbook 4f arrangement that should make the system telecentric

> **Quick definition - telecentric:** a system is telecentric when its entrance and/or exit pupil sits at infinity, meaning the chief ray travels parallel to the optical axis rather than converging toward a point. The practical payoff is that magnification stays constant even if the image plane shifts slightly out of focus, which is exactly why telecentric relays are the standard choice for metrology and confocal imaging, where you can't tolerate magnification drifting with small focus errors.

**Result - Config A (stop at Lens 1):**
This one worked cleanly on the first try.

![Figure 1: Lens data, spot diagrams, and 3D layout for the baseline two-lens relay with the stop at Lens 1](/assets/week1/configA_working.png)
*Lens Data, Spot Diagram (on-axis and 1° field), and 3D Layout for Config A. RMS radius: 152.985 µm on-axis, 187.436 µm off-axis.*

Both lenses' spherical aberration stacks (nearly 17× worse than Week 1's single-lens RMS of 9.032 µm), and the 1° off-axis field shows visibly more spread than on-axis — coma showing up for the first time now that there's an actual field angle to image, not just a single collimated point.

**Result - Config B (stop at the Fourier plane): two failures before it worked**

Moving the stop into the middle of the 192 mm gap broke the system twice, for two different, genuinely instructive reasons.

*Failure 1 - aperture type incompatible with an object-space-telecentric system:*

![Broken aperture state after moving the stop to the Fourier plane, with collapsed clear semi-diameters and a chief-ray trace error](/assets/week1/configB_epd_fail.png)
*Clear semi-diameters collapse to ~0.15 mm at the lens surfaces while the object surface balloons to 75 mm, and the spot diagram fails outright.*

Because the object sits exactly at Lens 1's front focal plane, this exact geometry is **object-space telecentric** - the entrance pupil (imaged back from the new stop position) sits at infinity. "Entrance Pupil Diameter" as an aperture type needs a finite pupil location to mean anything, so the paraxial solver degenerated. Fix: switch **Aperture Type to Object Space NA** (value 0.052, matching the original 10 mm EPD at 96 mm object distance).

*Failure 2 - Field Type incompatible with a finite-conjugate object:*

![Field data editor showing Angle-type fields causing a chief ray trace failure even after fixing the aperture type](/assets/week1/fieldtype_diagnosis2.png)
*Even with the aperture fixed, the 1° off-axis field still failed - the Field Type was set to "Angle," which only makes sense for an object at infinity.*

My object is at a *finite* 96 mm, not infinity, so "1° field angle" was a contradiction the solver couldn't resolve - it degenerated harmlessly at (0°,0°) but broke immediately off-axis. Fix: switch **Field Type to Object Height**, entering the off-axis field as an actual 1 mm object height rather than an angle.

With both fixes in place, the ray trace finally produces the textbook 4f "bowtie" - I added a third, mirrored field (−5, −5 mm) to get the full symmetric picture:

![Corrected 3D layout of the telecentric 4f relay showing the characteristic three-color bowtie ray pattern crossing at the Fourier plane](/assets/week1/telecentric_bowtie.png)
*3D Layout after both fixes, with three fields - on-axis (blue) and two symmetric off-axis bundles (green, red) cross cleanly through a shared Fourier plane.*

**Quantitative check:** on-axis RMS radius came out to 152.886 µm - essentially identical to Config A's 152.985 µm. That's expected, not a disappointment: telecentricity is about pupil location, not aberration correction, so it was never going to shrink the spot. The real payoff shows up in a Through-Focus Spot Diagram instead:

![Through-focus spot diagrams for all three fields across a defocus sweep, showing minimal change in spot size or position](/assets/week1/through_focus.png)
*Through-Focus Spot Diagram, ±100 µm defocus sweep, all three fields. Spot size and position barely shift across the sweep.*

That stability under defocus - not a smaller spot, but a spot that doesn't move or rescale as focus drifts - is the actual, tangible benefit of telecentricity, and now I can see it directly instead of just asserting it from theory.

**What surprised me:**
Neither of the two earlier failures was a data-entry mistake - both were the *solver correctly refusing to compute something physically undefined* given my settings. That's a different debugging mindset than I'm used to from lab equipment, where an error usually means broken hardware. Here, an error can mean "your system just became telecentric and your settings didn't know that." Field Type (Angle vs. Object Height) turned out to silently encode an assumption about object conjugate distance - mixing it up doesn't give a wrong-but-plausible answer, it just refuses to trace at all, which in hindsight is the safer failure mode.

**Open items for next time:** I still want to run the mis-spacing sensitivity test - deliberately moving the lenses off the 2f spacing by ±10% and recording how fast the image degrades, the simulated version of nudging a lens along a rail.

**Files:** [Week 2 project files on GitHub](https://github.com/spark-violet/zemax-learning-journal/tree/main/01_ray_optics) - `1-2_two_lens_4f_relay.zmx`

---
*Full lab-notebook-style writeup (Aim, Theory, Method, Results, Observations, Inference, Next Steps) available on [GitHub](https://github.com/spark-violet/zemax-learning-journal/tree/main/01_ray_optics/experiment-2-two-lens-4f-relay).*
