+++
title = "Trees in Embedded Systems!"
date = "2023-04-21"

author = "Piyush Itankar"
authorImage ="teams/piyush.jpg"
preferred = "https://x.com/dstreetdogg"
linkedin = "https://www.linkedin.com/in/streetdogg/"
twitter = "https://x.com/dstreetdogg"
blog = "itankar.com"
email = "piyush@inpyjama.com"

tags = [
    "dsa",
]
categories = [
    "dsa",
]

series = ["tree"]
images = ["/post/trees/1.jpg"]
+++

Data structures are extensively used in Embedded systems! This post talks about the use of trees in power management.
<!--more-->

Data structures are extensively used in Embedded systems! Any time you have to manage relations and dependencies between different hardware components in software, you will end up solving a graph problem and implementing the associated algorithms like - DFS, BFS, Shortest Path etc! Here’s an example this very common power management scenario -

![](1.jpg "fig 1. Representation of various logical blocks connected to the power line. `1`..`8` represent switches.")

The switches connecting the main switch `1` to the different IP blocks `P`, `Q`, `R`, `S`, `T` form a graph known as a tree. In this tree, the main switch `1` is the root, and the IP blocks `P`, `Q`, `R`, `S`, `T` are the leaves. The switches `2`, `3`, `4`, `5`, `6`, `7`, and `8` are the nodes or branches that connect the root to the leaves.

To turn on the different IP blocks, the switches must be turned on in the correct order. For example, to turn on IP block `P`, switches `1`, `2`, and `4` must be turned on in that order. Similarly, assuming every other IP is already off, to turn off IP block `P`, switches `4`, `2`, and `1` must be turned off in that order to ensure no power leaks of any sort.

It's worth noting that a node can be turned off only if all of its child nodes are already off. For instance, if switch `2` is turned on to activate IP block `P` or `Q`, it can only be turned off after switch `4` or `5` (child nodes) has been turned off first. Likewise, if switch `3` is turned on to activate IP blocks `R`, `S`, or `T`, it can only be turned off after all of its child nodes (switches `6`, `7`, and `8`) have been turned off.

This rule is essential to maintain the integrity of the IP blocks and the overall system. Turning off a node before its child nodes have been powered down can cause the loss of data and potential damage to the system, including the IP blocks.
