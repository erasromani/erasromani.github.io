---
layout: page
title: Robotic Simulation
description: Python wrapper for NVIDIA Omniverse Isaac-Sim
img: /assets/img/projects/isaac-sim.png
importance: 3
category: research
---

## Motivation

Before a robot ever touches a real object, it must first learn to interact safely in simulation.  
Robotic grasping — especially in cluttered environments — is one of the most challenging tasks in manipulation. It requires reasoning about geometry, contact forces, and control precision.

High-fidelity physics simulators like **NVIDIA Omniverse Isaac-Sim** make it possible to replicate these interactions at scale, but their interfaces are often complex and difficult to extend. I built **isaac-sim-python** as a flexible Python layer on top of Isaac-Sim, designed to make it _easy to prototype and visualize grasp executions programmatically._

---

## From API Complexity to Expressive Simulation

Isaac-Sim provides an incredibly detailed robotics simulation stack — realistic lighting, material physics, GPU-accelerated rendering, and reinforcement learning integration. Yet, scripting even simple tasks like moving a robotic arm or spawning random objects often requires verbose, boilerplate-heavy code.

The **isaac-sim-python** package abstracts that complexity.  
With a few lines of Python, you can initialize a Panda robotic arm, drop random objects into a bin, and simulate planar grasps from configurable positions and orientations.

The result is a framework that allows for:

- **Rapid experimentation** with grasp configurations and object types.
- **Automated scene generation** with randomized layouts for learning-based methods.
- **Video rendering** of simulated grasps for analysis or presentation.

---

## The Simulation

Each run begins with the Panda robotic arm suspended above a bin of randomized rigid objects.  
The simulation then executes a **planar grasp** — positioning the gripper over the target area, rotating to the desired approach angle, and descending smoothly to make contact.

The grasp succeeds or fails depending on the object geometry, mass, and surface friction parameters defined in the scene.  
A recording module captures the simulation and exports it as a short video clip, creating a visually intuitive way to debug or benchmark grasp behavior.

---

## Insights

Developing this wrapper highlighted the trade-offs between **simulation realism** and **control simplicity**.  
While Isaac-Sim offers unparalleled physical fidelity, it requires meticulous configuration to behave predictably across randomized runs.

Some of the key lessons:

- Scene initialization order and object mass scaling significantly influence simulation stability.
- Frame synchronization is essential when rendering videos at real-time frame rates.
- Small perturbations in grasp angle can produce large variations in outcome — an important property when training reinforcement learning policies.

These insights have shaped how I think about designing simulation pipelines for robotic learning — emphasizing **modularity**, **observability**, and **repeatability**.

---

## Looking Ahead

This project is an early step toward a broader goal: building simulation pipelines that bridge **robotic perception, grasp planning, and learning-based control**.  
Future directions include:

- Integrating tactile feedback and force sensors into the simulation loop.
- Using reinforcement learning to optimize grasp success rates across randomized object distributions.
- Extending the wrapper for multi-object reasoning and cluttered manipulation tasks.

---

## Repository

The full implementation and source code are available here:  
👉 [**GitHub — erasromani/isaac-sim-python**](https://github.com/erasromani/isaac-sim-python)

---

### Acknowledgments

Developed as part of ongoing research exploring simulation-based learning for robotic manipulation and grasp optimization.
