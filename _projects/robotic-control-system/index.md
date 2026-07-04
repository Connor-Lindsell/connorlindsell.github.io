---
layout: post
order: 70
title: "Robotic Control System Simulation and Integration"
description: Developed a C++ control system for simulated autonomous robotic platforms, using ROS 2, threaded mission logic, and modular controllers for aerial and ground robots.
skills:
  - C++
  - ROS 2
  - Threading
  - Robotics Simulation
  - Doxygen
  - Modular Control
main-image: /robotic-control-simulation.png
---

## Overview

This university project focused on developing a control system for autonomous robotic platforms in a simulated environment. Using C++ and the pfms2ros simulation framework, the system controlled both a quadcopter and an Ackermann-steered ground robot.

## Technical Work

- Implemented threaded mission logic for multiple simulated platforms.
- Developed platform-specific controllers for scanning, movement, and goal-reaching behaviour.
- Built modular controller classes to manage mission state, timing, and platform transitions.
- Focused on smooth motion handling, synchronisation, and accurate stopping conditions.
- Documented the codebase using Doxygen for clarity and future development.

## Outcome

The project strengthened my understanding of structured C++ programming, multithreaded control logic, and modular software design for robotics applications.

## Repository

GitHub: <https://github.com/Connor-Lindsell>

{% include image-gallery.html images="robotic-control-simulation.png" height="350" %}
