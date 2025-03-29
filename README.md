# The Project Goal

## General Project

Working on building a router (simulated) that, given a 3d shape, determines the optimal way to cut out stock around that intended shape as quickly as possible. The goal is that, given say a 20inch by 20inch piece of stock and a cylinder with 3 inch radius, the router will determine the ideal moves to cut out the stock necessary to be left with just the cylinder. I am beginning with a simulated environment, and I would like to use reinforcement learning for this task. 

## Movement

Each move can cover multiple pixels and does not necessarily have to be linear, for example it could move in a hemisphere. We also want to be able to perform complex paths that are chained together in steps, as we may need great precision to carve out complex objects with the cnc. We will create two separate subgoals (heirarcical), one bigger picture and one smaller picture to generate those precise paths to carve out the regions specified by the bigger picture reinforcement learning agent. This technique is best emulated using feudal reinforcement learning. Thus, we have a manager and a worker node. The manager specifies spatial subgoals (Clear this region, move to this region etc of the stock). Then, the worker learns to execute small, accurate moves. This separation lets you tailor the reward structure so that the high-level policy focuses on overall shape precision and efficiency, while the low-level controller focuses on safe, precise movements.  
Why is feudal reinforcement leraning a good approach  

1. provides clear temporal abstraction (simplifies complex, time-dependent data or processes by representing them at a higher level of detail, focusing on key patterns and relationships rather than individual events)  
2. separates long-horizon planning (overall shape carving) from low level execution (small subsection carving)  
3. allows the designation of specific subgoal rewards that align with the precise nature of cnc machining.

## Implementation details
