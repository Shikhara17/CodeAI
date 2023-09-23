# CodeAI

## Pathfinding Agents
Implemented  A* algorithm with which agent can find a path from current location to Townhall.

### Changes made in the existing code
   1) **MapLocation class**: Added two new fields in the existing class i.e heuristicCost,f. Modified the constructor so that it assigns values for cameFrom and cost during initialization.
   2) **terminalStep:** System.exit() is invoked in the terminalstep function in order to stop the execution after logging "Destroyed the TownHall" in case of dynamic maps.
     
### Agent's Functionality:
   1) **AstarSearch():**
   2) **expandNextAvailableValidSteps():**
   3) **calculateChebyshevDistance():**
   4) **configurePath():**
   5) **shouldReplanPath():**


