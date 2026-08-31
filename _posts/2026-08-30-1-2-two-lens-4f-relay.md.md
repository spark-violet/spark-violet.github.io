---
layout: post
title: "1.2. Two-Lens 4f Relay"
date: 2026-08-30
categories: [ray-optics, system-design]
---

**Problem:** This work extends Project 1's single lens into a **4f relay**. The two identical lenses arranged so a point at the front focal plane of Lens 1 gets re-imaged at the back focal plane of Lens 2, with a shared "Fourier plane" exactly midway between them. This is the same geometry behind Fourier-space and confocal imaging setups I've aligned by hand during my PhD.

**Approach:**
I reused Project 1's exact lens prescription (plano-convex N-BK7, R = 50 mm, EFFL ≈ 96.25 mm) for both elements, placed the object 96 mm in front of Lens 1 (its front focal plane), spaced the two lenses 192 mm apart (2f), and set the final image distance to another 96 mm. I built and compared two configurations:

- **Config A** - aperture stop left at Lens 1
- **Config B** - aperture stop moved to the Fourier plane, the textbook 4f arrangement

> **Quick definition - telecentric:** a system is telecentric when its entrance and/or exit pupil sits at infinity, meaning the chief ray travels parallel to the optical axis rather than converging toward a point. The practical payoff is that image behavior stays consistent even if the image plane shifts slightly out of focus.

**Getting Config B working took two real debugging steps**, both worth documenting because neither was a data-entry mistake - both were Zemax correctly refusing to compute something physically undefined given my settings:

![Broken aperture state after moving the stop to the Fourier plane, with collapsed clear semi-diameters and a chief-ray trace error](/assets/week1-1/configB_epd_fail.png)
*Moving the stop to the Fourier plane makes the system object-space telecentric (entrance pupil at infinity) - which breaks "Entrance Pupil Diameter" as an aperture spec. Fix: switch to Object Space NA.*

![Field data editor showing Angle-type fields causing a chief ray trace failure even after fixing the aperture type](/assets/week1-1/fieldtype_diagnosis2.png)
*Even after fixing the aperture, the off-axis field still failed - Field Type was "Angle," which only makes sense for an object at infinity. My object is at a finite 96 mm. Fix: switch to Object Height fields.*

**Then a third, quieter bug nearly wrecked the comparison entirely:** my first attempt to compare Config A and B used slightly different image distances (96.000 mm vs. 93.212 mm) because of two independent Quick Focus solves landing in different places. That alone was enough to make the configurations look very different for the wrong reason. Lesson: **before comparing two systems, verify every shared parameter matches exactly** - not just the one you meant to change.

**The real comparison - matched fields, matched image distance:**

With both bugs fixed and both configs locked to identical field points (0; ±5, ±5 mm object height) and identical image distance, here's what stop position actually does:

| Field | Config A (stop at Lens 1) | Config B (stop at Fourier plane) | Ratio |
|---|---|---|---|
| On-axis | 152.985 µm RMS | 152.886 µm RMS | 1.00× |
| Off-axis (±5,±5) | **1311.07 µm RMS** | **346.729 µm RMS** | **3.78× worse in A** |

On-axis, stop position changes essentially nothing. Off-axis, it's nearly a 4× difference. You can see exactly why in the ray geometry itself:

![Config A 3D layout at matched fields, showing the off-axis bundles striking Lens 2 asymmetrically](/assets/week1-1/configA_matched_layout.png)
*Config A: because the stop sits at Lens 1, off-axis bundles (green/red) hit Lens 2 off-center and at a steep angle - the direct geometric cause of coma.*

Compare that to Config B's ray geometry, symmetric about the stop:

![Config B 3D layout showing the telecentric 4f bowtie pattern, symmetric about the Fourier plane](/assets/week1-1/telecentric_bowtie.png)
*Config B: stop at the Fourier plane makes the whole system symmetric about that plane — every field's bundle crosses the same shared point and hits Lens 2 as a mirror image of how it left Lens 1. That geometric symmetry is what cancels coma.*

> **Why on-axis doesn't care but off-axis does:** for an on-axis point, the chief ray *is* the optical axis no matter where the stop sits — so on-axis image quality (dominated by spherical aberration here) barely notices stop position. Off-axis, stop position decides which part of each lens the oblique bundle actually passes through. Put the stop asymmetrically (Config A) and you get **coma** — the classic comet-shaped off-axis blur. Put the stop at the symmetry point of an otherwise-symmetric system (Config B — same two lenses, same spacing, mirrored about the stop) and coma cancels almost by geometry alone. This is the actual reason symmetric lens designs were the standard approach long before computers could optimize aspheric surfaces.

The through-focus spot diagrams make the difference impossible to miss:

![Config A through-focus spot diagrams at matched field points, showing pronounced comet-shaped coma](/assets/week1-1/configA_through_focus.png)
*Config A: off-axis spots are strongly elongated at every defocus position (note the 5× larger scale bar vs. Config B below — these spots are genuinely much bigger, not a plotting trick).*

![Config B through-focus spot diagrams, much smaller and defocus-insensitive](/assets/week1-1/through_focus.png)
*Config B: off-axis spots are visibly smaller and barely change shape or size across the same defocus sweep.*

**What surprised me:** I went into this expecting telecentricity (consistent behavior under defocus) to be the headline result of moving the stop. It turned out coma suppression - a completely different piece of theory, about symmetry rather than pupil location - is the bigger, more immediately obvious effect at this field size. Both are real and both come from the same single change (relocating the stop), but they're not the same phenomenon, and I'd have conflated them if I hadn't run the matched comparison carefully.

**Open items for next time:** run the mis-spacing sensitivity test (±10% inter-lens spacing) on both configs, and run a proper through-focus sweep on Config A too so the telecentricity claim is as quantitative as the coma claim now is.

**Files:** [Project 2 files on GitHub](https://github.com/spark-violet/zemax-learning-journal/tree/main/01_ray_optics/project-2-two-lens-4f-relay) — `phase1_project2_two_lens_4f_relay.zmx`

---
*Full lab-notebook-style writeup (Aim, Theory, Method, Results, Observations, Inference, Next Steps) available on [GitHub](https://github.com/spark-violet/zemax-learning-journal/tree/main/01_ray_optics/project-2-two-lens-4f-relay).*
