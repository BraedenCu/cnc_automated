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

The implementation begins with developing a simulated 3D environment that represents both the stock and the target shape as voxel grids. The router is simulated as an agent that can remove material along its path, where each move is not confined to a simple linear displacement but can span multiple voxels and follow complex, non-linear trajectories. To achieve precise carving, the project employs a feudal reinforcement learning framework with a hierarchical structure. The high-level manager sets spatial subgoals—such as designating which region of the stock to clear—while the low-level worker learns to execute the fine, accurate movements required to meet these subgoals. This separation allows the system to abstract the temporal complexity of long-horizon planning from the intricacies of short, precise actions, and enables the design of specialized subgoal rewards that align with the precise demands of CNC machining. Overall, the system iteratively trains the agent in this environment, refining both levels of control until the router can optimally remove the unnecessary stock and leave behind only the intended shape.

higher level manager, low level worker --> operate on diff scales and receive different reward signals.

### manager network

input --> downsampled version of the full voxel grid.
output --> subgoal vector representing spatial target or desired change in state. Will output relative position that the worker should try to clear!
time scale --> manager operates at a slower rate (every T steps, where T is a fixed number of steps, or potentially dynamic!) so that it sets higher level goals that span many levels

### worker network

input --> local observations (or full state if possible!) concatenated with the subgoal provided by the manager. This helps the worker focus on acheving the current subgoal set by the manager  
output --> low-level actions which could be discrete (small directional moves) or continuous that move router a small distance at each step  
reward --> workers reward is structured around how well it achieves the given subgoal (i.e the dist to the subgoal or progress made toward clearing the region)  

### reward decomposition and training signals

manager reward --> manager receives a global reward based on overall progress towards the final carving objective (such as portion of unecessary stock removed while leaving target shape intact), potentially bonus if higher level goal is met (worker clears specified region entirely)  
worker reward --> worker is given an intrinsic reward for making progress towards the subgoal. maybe this is measuring distance between routers current position and the subgoal, and we need some penalty for each step to encourage efficiency to meet the task.

### temporal abstraction and communication

The two networks communicate via the subgoal: The manager produces a subgoal every T steps (or continuously updated every step but only enforced every T steps), and the worker’s performance is evaluated with respect to that subgoal.  
FeUdal Networks (FuN) approach, where the manager uses a directional “goal embedding” that the worker learns to follow.