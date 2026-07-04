---
layout: post
order: 30
title: "Pliers Design Challenge for Additive Manufacturing"
description: Designed, topology-optimised, 3D printed, and failure-tested a lightweight pair of pliers, improving strength while reducing mass through iterative DfAM refinement.
skills:
  - SolidWorks
  - Topology Optimisation
  - Blender
  - FDM 3D Printing
  - Creality Print
  - Stress Testing
  - Design for Additive Manufacturing
main-image: /final-pliers-design.png
---

## Overview

This university project involved the design, optimisation, and testing of a pair of pliers using additive manufacturing techniques. The objective was to reduce weight while maintaining structural integrity through topology optimisation, then experimentally validate performance through controlled failure testing.

## Design and Optimisation

- Modelled the initial plier geometry in SolidWorks and applied topology optimisation under load cases representative of typical plier use.
- Removed low-stress regions to create a lightweight, lattice-inspired form.
- Refined the optimised mesh in Blender to improve manufacturability.
- Tuned Creality Print 6 slicer settings, including infill patterns, localised infill density, and support strategy, to balance mass reduction with structural performance.

## Testing and Evaluation

More than six PLA prototypes were produced using fused filament fabrication. Each prototype was tested with a force meter until mechanical failure, with results feeding into the next design iteration.

| Iteration | Effective Weight | Failure Load |
|---|---:|---:|
| Third iteration | 35.2 g | 64.6 N |
| Final iteration | 27.6 g | 117.5 N |

The final design achieved a 22% effective weight reduction and an 82% strength increase compared with the earlier tested iteration. Testing also highlighted critical fracture points and showed how print orientation and layer adhesion limited performance.

## Outcome

This project reinforced the importance of aligning optimisation algorithms with real-world loading conditions. It also showed how slicer strategy, mesh cleanup, and material limitations directly affect practical additive manufacturing outcomes.

## Media

Video link: <https://youtu.be/QGNRDCJP024>

{% include image-gallery.html images="final-pliers-design.png, prototype-iteration-1.png, prototype-iteration-2.png, prototype-iteration-3.png" height="320" %}

{% include image-gallery.html images="prototype-testing.jpg, video-thumbnail.png" height="260" %}
