---
layout: post
order: 10
title: "WordleBot: Layered RL Decision Making and Robot Motion Control"
description: Integrated UR3e Wordle-playing robot with a high-level MaskablePPO decision model, ROS 2 task sequencing, and low-level MoveIt 2 pick-and-place control.
skills:
  - ROS 2
  - UR3e
  - MoveIt 2
  - MaskablePPO
  - Reinforcement Learning
  - Motion Planning
  - Pick and Place
main-image: /tensor_board.png
---

## Overview

WordleBot is a layered robotic manipulation system designed to autonomously arrange physical letter blocks into a requested Wordle word. The system combined perception, word/game logic, reinforcement learning, ROS 2 integration, MoveIt 2 motion planning, UR3e robot execution, and OnRobot RG2 gripper control.

My contribution sat across two connected technical layers. The high-level decision model was developed through Artificial Intelligence for Robotic Systems and trained as a reinforcement learning task sequencer. The low-level motion control package was developed through Robotics Studio 2 and converted those task decisions into collision-aware robot pick-and-place behaviour.

The two parts are presented together because they form one control pipeline:

```text
Word/game state -> RL task sequencing -> ROS 2 high-level control -> MoveIt 2 / UR3e / RG2 pick-and-place
```

Quick links: [High-Level Decision Model](#high-level-decision-model), [Low-Level Motion Control](#low-level-motion-control), [Integration and Results](#integration-and-results)

## Demo Media

The videos below show the system moving beyond simulation and into live demonstration. The first clip shows the perception side identifying physical letter blocks, while the second shows the Wordle gameplay flow where the control system moves blocks according to the selected route.

<div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:1rem;margin:1.5rem 0;">
  <div>
    <video controls playsinline style="width:100%;border-radius:8px;">
      <source src="/projects/wordlebot/demo-cnn-detection.mp4" type="video/mp4">
    </video>
    <p><em>Live demo: CNN letter detection and board-state input.</em></p>
  </div>
  <div>
    <video controls playsinline style="width:100%;border-radius:8px;">
      <source src="/projects/wordlebot/demo-robot-pick-place.mp4" type="video/mp4">
    </video>
    <p><em>Live demo: Wordle gameplay with robot pick-and-place movement.</em></p>
  </div>
</div>

## My Role

I focused on the control side of WordleBot: training and evaluating the reinforcement learning sequencer, integrating the high-level task output with ROS 2, and developing the low-level motion planning behaviour needed to execute ordered pick-and-place actions on the UR3e.

This included:

- Designing and training a MaskablePPO policy for Wordle board task sequencing.
- Comparing the trained policy against a greedy nearest-neighbour baseline.
- Building the bridge between requested words, board state, and robot action queues.
- Implementing staged pick-and-place execution using ROS 2, MoveIt 2, and the RG2 gripper.
- Adding mission-control behaviour for Start, Stop, Resume, and Abort workflows.
- Testing collision-aware movement, attached-object handling, and recovery behaviour on real robot hardware.

<a id="high-level-decision-model"></a>

## WordleBot High-Level Decision Model

The high-level decision model decides *what the robot should move next*. It does not directly command joints or generate trajectories. Instead, it receives a board state and target word, then outputs an ordered sequence of pick-and-place decisions for the low-level controller to execute.

Training work was developed in the RL repository:

- RL training repository: <https://github.com/Connor-Lindsell/AiRobotics>
- Local training module: `D:\git\AiRobotics\rl_task_optimiser`
- ROS high-level package: <https://github.com/LukeFKlusman/WordleBot/tree/e3d0511bf68eb2beb2a5abffa73fb99d1d11f7da/motion_planning_and_control/hl_control>
- High-level technical documentation: <https://github.com/LukeFKlusman/WordleBot/wiki/WordleBot-High-Level-Control>

### Reinforcement Learning Formulation

The task was modelled as a discrete sequencing problem over a 13 by 7 gameboard. Each action represented a source cell and destination cell pair, allowing the agent to choose which letter block to move and where to place it.

Key design choices:

- **Algorithm:** MaskablePPO from `sb3-contrib`.
- **Action space:** discrete source/destination pick-and-place pairs.
- **Observation:** robot position, occupied cells, letter identities, correct placement flags, target-word encoding, and curriculum stage.
- **Action masking:** invalid moves were masked before policy sampling so the agent learned only from legal task decisions.
- **Reward shaping:** step cost, travel distance penalty, correct-placement reward, clearing reward, completion bonus, and greedy-baseline competition bonus.
- **Baseline:** deterministic nearest-neighbour solver that selected the valid move with the lowest immediate travel distance.

The important engineering boundary was that reinforcement learning owned task sequencing only. MoveIt 2 and the low-level control package remained responsible for trajectory generation, inverse kinematics, collision checking, gripper control, and hardware execution.

### Curriculum Training

Training used a staged curriculum so the policy learned from simpler board states before facing the full clear-and-place task.

| Stage | Task focus | Purpose |
|--------|------------|---------|
| C1 | Single correct letter | Learn the basic pick-and-place structure. |
| C2 | Multiple correct letters and distractors | Learn to select useful blocks while ignoring irrelevant ones. |
| C3 | Wordle slots pre-filled with wrong letters | Learn to clear the board before placing the correct word. |
| C4 | Looser action mask on the same style of board | Push more responsibility onto the learned reward-driven policy. |

The final portfolio results focus on C3, the hardest curriculum stage reached and evaluated for the submitted system. In this scenario all five Wordle slots were already occupied by incorrect letters, so the agent had to clear obstructing blocks before placing the target word.

### High-Level Results

| Metric | Result |
|--------|--------|
| RL average travel distance | 48.5 m |
| Greedy baseline average travel distance | 51.1 m |
| C3 task success rate | 100%, 20 / 20 episodes |

The trained RL policy completed all C3 evaluation episodes and reduced average travel distance compared with the greedy baseline. This showed that the agent was learning a more globally efficient task sequence rather than only choosing locally short moves.

{% include image-gallery.html images="tensor_board.png" height="350" %}

{% include image-gallery.html images="distance_bar_graph.png, Cumulative_distance.png" height="320" %}

{% include image-gallery.html images="c3_clear_and_place_ep4_animation.gif" height="320" %}

<a id="low-level-motion-control"></a>

## WordleBot Low-Level Motion Control

The low-level motion control package decides *how the robot safely executes each requested move*. It takes the ordered task sequence from the high-level layer and converts each item into a staged pick-and-place routine for the UR3e and RG2 gripper.

Primary references:

- Low-level control package: <https://github.com/LukeFKlusman/WordleBot/tree/e3d0511bf68eb2beb2a5abffa73fb99d1d11f7da/motion_planning_and_control/wordlebot_control>
- Low-level technical documentation: <https://github.com/LukeFKlusman/WordleBot/wiki/Wordle-Bot-Low-Level-Control>
- Robotics Studio 2 final report: `WordleBot Final Report.docx`

### Motion Planning Architecture

The low-level controller was implemented as a ROS 2 and MoveIt 2 control layer for the UR3e. Its job was to turn each selected block movement into repeatable robot behaviour:

- Open the RG2 gripper and move to a safe pre-pick pose.
- Approach the detected block pose.
- Close the gripper and attach the object in the planning scene.
- Lift and transfer the object while accounting for attached-object collision geometry.
- Move to a pre-place pose over the target Wordle slot.
- Descend, release, detach the object, and retreat safely.

This staged structure was necessary because reliable robotic pick-and-place requires more than moving directly from one Cartesian pose to another. The controller had to manage robot state, gripper state, object state, collision objects, and recovery behaviour throughout the mission.

### MoveIt 2 Execution and Recovery

The final controller used MoveGroupInterface-style execution for stronger control over inverse kinematics, candidate trajectory generation, orientation handling, and plan filtering. During development, MoveIt Task Constructor was explored for long-horizon task planning, but the final system prioritised reliable 4-DOF block placement and repeatable hardware execution.

Key low-level capabilities included:

- Point-to-point end-effector motion through the UR3e planning interface.
- Waypoint movement and staged pick-transfer-place execution.
- Static collision objects in the gameboard workspace.
- Attached-object collision handling after the gripper picked up a block.
- Candidate trajectory scoring to reduce unnecessary joint movement and awkward wrist configurations.
- Yaw recovery for collision-prone place poses.
- Variable-speed behaviour when the end effector was near collision-sensitive areas.

### Mission Control

The control package also included mission-level behaviour so the robot could be operated as a system rather than as isolated motion commands.

| Command | Behaviour |
|---------|-----------|
| Start | Begins execution of the queued pick-and-place sequence. |
| Stop | Interrupts active motion and holds the current mission state. |
| Resume | Continues the remaining task queue after interruption. |
| Abort | Stops active work, clears the queue, and returns toward a safe state. |

This mattered because WordleBot was demonstrated on physical hardware with multiple ROS 2 subsystems active at once. Predictable interruption and recovery behaviour made the system safer and easier to integrate during final testing.

## Integration and Results

The final control architecture linked high-level task reasoning to low-level robot execution:

- The high-level RL agent selected the order of board operations.
- The ROS high-level control layer translated the selected word and board state into executable task requests.
- The low-level control package planned and executed each pick-and-place movement using MoveIt 2, the UR3e, and the RG2 gripper.

This integration was the reason the two class contributions worked best as one portfolio project. The AI module was valuable because it chose an efficient sequence across the whole board, while the Robotics Studio control package was valuable because it made those decisions physically executable.

The strongest integration outcome was that the GUI, gamification, high-level control, and low-level motion-control pipeline worked through the intended ROS 2 topic structure. The main limitation was upstream perception pose reliability: letter classification worked, but final autonomous execution needed more consistent world-frame block positions, camera-to-robot calibration, block orientation, and object IDs. Rather than hard-coding the control system, the team used a fallback board state published through the same expected interface, which preserved the architecture and allowed the RL sequence and UR3e execution pipeline to be validated honestly.

The final subsystem achieved the HD-level intent of the Robotics Studio 2 motion-planning contract: waypoint movement, path planning, collision-aware pick-and-place, 4-DOF object handling, gripper control, mission-state control, and task-sequence optimisation. It did not claim full dynamic collision avoidance, because the robot did not continuously regenerate paths around moving hazards during execution.

### Hardware Evidence

The final report evidence showed the control stack operating on the physical UR3e workcell, not only in the RL simulation environment. The images below show the board before and after a hardware placement run, plus live demonstration frames with the UR3e, letter blocks, and gameboard in the workspace.

{% include image-gallery.html images="hardware-start-poses.jpeg, hardware-finish-poses.jpeg" height="360" %}

{% include image-gallery.html images="live-demo-placement.jpg, live-demo-ur3e.jpg" height="320" %}

The gameboard itself was designed around a 13 by 7 cell layout. This gave the high-level policy a discrete planning grid while still mapping cleanly to physical pick-and-place targets for the low-level controller.

{% include image-gallery.html images="gameboard-layout.jpeg" height="330" %}

## Reflection

The strongest lesson from WordleBot was that robotic intelligence needs both decision quality and execution reliability. A good sequence is not useful if the robot cannot pick, transfer, place, stop, recover, and avoid collisions. At the same time, a reliable low-level controller is limited if it is only given short-sighted or manually scripted actions.

Splitting the project into a high-level decision model and low-level motion controller made the architecture easier to reason about. The RL layer answered *what should happen next?* The motion-control layer answered *how can the robot do that safely and repeatably?* Together they formed a complete manipulation pipeline rather than two disconnected course artefacts.

A major technical trade-off was moving away from MoveIt Task Constructor as the final execution backend and using MoveGroupInterface-style control instead. MTC was attractive for long-horizon task planning, but MGI gave stronger control over exact placement poses, inverse kinematics, orientation filtering, and recovery behaviour. For this project, reliable 4-DOF pick-and-place mattered more than preserving a cleaner theoretical planning framework.

The hardware work also made the integration challenge very clear. UR3e bring-up, ROS domain setup, robot IP configuration, stale nodes, driver state, gripper state, and network reliability could all affect whether the system worked on the day. The most important process lesson was that robotics teams need earlier interface contracts, dummy publishers/subscribers, ROS bag evidence, and regular hardware dry runs. A subsystem is only truly complete when its assumptions have been tested against the rest of the robot.

The other important collaboration lesson was that a system can have strong individual subsystems and still fall short of full autonomy if one interface is not verified early enough. In this project, the control side was ready to consume perception outputs and execute the selected tasks, but the final perception-to-control pose pipeline was not reliable enough to claim a perfect end-to-end autonomous system. A better process would have defined measurable interface gates earlier, such as requiring perception to publish validated world-frame block poses on the agreed topic before final integration week.

## Source Links

- RL training repository: <https://github.com/Connor-Lindsell/AiRobotics>
- WordleBot repository: <https://github.com/LukeFKlusman/WordleBot>
- High-level ROS package: <https://github.com/LukeFKlusman/WordleBot/tree/e3d0511bf68eb2beb2a5abffa73fb99d1d11f7da/motion_planning_and_control/hl_control>
- High-level technical documentation: <https://github.com/LukeFKlusman/WordleBot/wiki/WordleBot-High-Level-Control>
- Low-level control package: <https://github.com/LukeFKlusman/WordleBot/tree/e3d0511bf68eb2beb2a5abffa73fb99d1d11f7da/motion_planning_and_control/wordlebot_control>
- Low-level technical documentation: <https://github.com/LukeFKlusman/WordleBot/wiki/Wordle-Bot-Low-Level-Control>
