---
layout: post
order: 40
title: "Parallel Coupled Line Filter for 5G Communications"
description: Designed and simulated a 3.4-3.8 GHz microstrip parallel coupled line bandpass filter for 5G communications using RF filter theory and ADS simulation.
skills:
  - Electronic Design
  - RF Design
  - ADS
  - Microstrip Filters
  - Circuit Simulation
  - 5G Communications
main-image: /filter-3d-model.png
---

## Overview

This individual university project focused on designing a microstrip parallel coupled line filter for 5G applications. The filter targeted the 3.4-3.8 GHz frequency range, with a centre frequency of 3.6 GHz.

## Technical Work

The project involved three main stages:

- Designing a Butterworth lowpass filter.
- Transforming the design into a lumped circuit bandpass filter.
- Implementing the filter as a microstrip parallel coupled line design.

The design was simulated in Advanced Design System (ADS) to verify that it met the required frequency response.

## Key Features

- A 3-pole Butterworth lowpass prototype used as the design basis.
- Parallel coupled line geometry for RF signal transmission and filtering.
- Iterative LineCalc adjustments to tune width, length, and spacing.
- Simulation results showing a moderate selectivity bandpass response across the target 5G band.

## Results

The final design met the intended specifications, producing a bandpass response centred at 3.6 GHz with bandwidth from 3.4 GHz to 3.8 GHz. The project demonstrated how RF and microwave engineering theory can be translated into a practical simulated filter design.

{% include image-gallery.html images="filter-3d-model.png, lumped-bandpass-schematic.png, microstrip-filter-schematic.png, simulation-results.png" height="300" %}
