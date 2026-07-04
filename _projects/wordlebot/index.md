---
layout: post
order: 110
title: "WordleBot: Reinforcement Learning and Robotic Path Planning"
description: Autonomous UR3e Wordle-playing system using perception, game logic, PPO reinforcement learning, and pick-and-place execution. Connor's focus was the RL and path-planning side of the robotic manipulation pipeline.
skills:
  - ROS 2
  - UR3e
  - PyBullet
  - MaskablePPO
  - Reinforcement Learning
  - Path Planning
  - Pick and Place
main-image: /tensor_board.png
---

## Overview

WordleBot is a robotic system designed to autonomously play Wordle using a UR3e robot arm. The full system combines computer vision, letter classification, game-state logic, reinforcement learning, and physical pick-and-place execution.

The project was split into parallel tracks. The perception and game logic pipeline identified letter blocks and target words, while the reinforcement learning and path-planning pipeline planned efficient robot movements for selecting, clearing, and placing blocks.

## My Role

My contribution focused on the reinforcement learning and robotic path-planning side of the project. This included PPO policy training, simulated pick-and-place task design, path efficiency evaluation, and integration with UR3e motion execution.

## Technical Work Completed

- Worked on a PyBullet-based simulation environment for training a UR3e-style pick-and-place policy.
- Used a MaskablePPO reinforcement learning agent to learn valid action sequencing under task constraints.
- Evaluated the trained policy against a greedy nearest-neighbour baseline.
- Supported integration between gameboard state, target word requests, and robot motion execution.
- Documented results using training curves, path-length comparisons, and episode-level distance metrics.

## Results

The trained policy was evaluated on the hardest curriculum stage reached, where all five Wordle board slots were pre-occupied and had to be cleared before the correct letters could be placed.

| Metric | Result |
|--------|--------|
| RL average travel distance | 48.5 m |
| Greedy baseline average travel distance | 51.1 m |
| C3 task success rate | 100%, 20 / 20 episodes |

The RL policy reduced average travel distance compared with the greedy baseline and successfully completed every C3 evaluation episode.

## Result Figures

{% include image-gallery.html images="tensor_board.png" height="350" %}

{% include image-gallery.html images="distance_bar_graph.png, Cumulative_distance.png" height="320" %}

{% include image-gallery.html images="c3_clear_and_place_ep4_animation.gif" height="320" %}

## Videos To Add Later

Large demo videos were intentionally not copied into this portfolio in the first pass. To add them later, place compressed files in `_projects/wordlebot/`, then embed them with HTML such as:

```html
<video controls playsinline>
  <source src="demo-video.mp4" type="video/mp4">
</video>
```

Candidate source videos are currently in `D:\git\AI_WordleBot_Submission\static\videos\`.

## Images To Add Later

Place additional WordleBot images inside `_projects/wordlebot/` and reference them with:

```liquid
{% raw %}
{% include image-gallery.html images="new-image-1.png, new-image-2.png" height="350" %}
{% endraw %}
```
