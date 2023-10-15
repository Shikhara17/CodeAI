# CodeAI
 `Refer SEPIA Documentation for the java packages used in this repository - http://engr.case.edu/ray_soumya/sepia/html/`
 <br/>
## P2Agents: Pathfinding

Implemented A\* algorithm with which agent can find a path from current location to Townhall.

### Changes made in the existing code

1.  **MapLocation class**: Added two new fields in the existing class i.e heuristicCost,f. Modified the constructor so that it assigns values for cameFrom and cost fields during initialization.
2.  **terminalStep:** System.exit() is invoked in the terminalstep function in order to stop the execution after logging "Destroyed the TownHall" in case of dynamic maps.

### Agent's Functionality:

1.  **AstarSearch():**
    The `AstarSearch` function executes the A\* search algorithm to determine the optimal path from a specific starting point to the goal and then destroy the town hall in a maze. It takes into account potential obstacles, the presence of enemy footmen, and the presence of resource locations during the path calculation. This function uses an **openSet** to store nodes that need to be expanded according to their actual path cost, and a **closedSet** to track the nodes that have been visited. <br/><br/>`AstarSearch` invokes other methods such as `configurePath()`, `calculateChebyshevDistance()`, and `expandNextAvailableValidSteps()` to find the optimal path.

2.  **expandNextAvailableValidSteps():**
    The `expandNextAvailableValidSteps` function expands the current node and generates all of its succesors. In this method we take currentLocation, goal, boundaries of map and set of resource Locations as input. It generates all the neighbors one by one and checks if they fall within the map's defined boundary and they are not already occupied by resources (i.e. trees). Only a valid Stack of neighbors would be returned by the function. This function internally calls the `calculateChebyshevDistance` to assign the heuristic value to each neighbor node.

3.  **calculateChebyshevDistance():**
    This`calculateChebyshevDistance` methods calculates the chebyshev distance between 2 locations which we use as a heuristic value for the current location

    This Method is invoked in the `AstarSearch` and `expandNextAvailableValidSteps` functions to determine the `heuristicCost`.

4.  **configurePath():**
    The `configurePath` function helps in forming a path from initial location to goal by backtracking from the goal node and adding their respective coordinates to the path. The function returns a `Stack<MapLocation>` representing the path from the initial state to the destination state.

5.  **shouldReplanPath():**
    The `shouldReplanPath` function within the A\* search is responsible for determining whether the current path should be replanned or not due to presence of enemyFootman in the path.

    It evaluates the current path, represented as a stack of `MapLocation` objects, to check if the `enemyFootman` is blocking the way. If the enemy is found in the path, the function returns `true`. Otherwise, it returns `false`.

    The `shouldReplanPath` function invoked with in `middleStep` method.

## P3Agents

### GameState

1. **GameState(State.StateView stateView):**
    The `GameState` constructor initializes key components for managing the state of a game. This comstructor initializes the movers and opponents lists, extracts unit information from the stateView object, Sets xExtent and yExtent to represent the dimensions of the game map. It also creates a set of obstacles based on the resource nodes from the stateView. It Initializes an AstarAgent and defines a list of valid directions to use in the game.
3. **public GameState(GameState previousGameState)**
    The `GameState` constructor in the code initializes the current GameState based from another GameState object. This constructor keeps track of the player's turns by incrementing it by one. This constructor has the logic of taking turns between players,i.e., movers and opponents.
4. **extractUnitInformation(State.StateView stateView**:
   The `extractUnitInformation` is used to retrieve footmen and archer units by using the information provided in the `StateView` object. This internally calls `extractUnitsFromUnitIds` to fetch the next state unit.
5. **extractUnitsFromUnitIds(List<Integer> unitIds,State.StateView stateView)**:
   The `extractUnitsFromUnitIds`, is called within the `extractUnitInformation` method to retrieve units based on their unitIds using the data provided by the `StateView` object. It creates a list of `NextStateUnit` objects, each representing a unit, by fetching unit information from the `StateView` based on the provided unitIds.
6. **getUtility():**
   The `getUtility` method calculates a utility value for the game state, considering the health of footmen and attackers, and the total distance to opponents. It adjusts calculations based on whether it's an even or odd turn, to view the utility of the respective player.
The utility is higher when footmen have more health, and it decreases with greater distance to opponents and higher opponent health. The formula used to compute utility is: `5 * (Footmen Health) - 10 * (Opponents Health) - 15 * (Total Distance to Opponents)`.
7. **calculateTotalDistance(List<NextStateUnit> footmen, List<NextStateUnit> archers):**
   The `calculateTotalDistance` method is used to calculate the cumulative distance between each footman and their closest archer in a game. The method iterates through each footman in the footmen list and for each footman, it finds the closest archer in the archers list using the `getClosestUnit` method. If there are obstacles on the map, it uses the A* algorithm to find the path from the footman to the closest archer and then the length of the path is added to the **totalDistance**. If there are no obstacles on the map or if the footman is adjacent to the archer, it calculates the distance using the Manhattan distance, and this is also added to the **totalDistance**. The total cumulative distance between all footmen and their closest archers is returned
9. **distanceToEnemy(NextStateUnit unit1, NextStateUnit unit2):**
   The `distanceToEnemy` method is used to calculate the Manhattan distance which is the sum of the absolute differences between their X-coordinates and Y-coordinates between two game units objects, and returns it.

9. **getChildren():**
   The `getChildren` method is used for creating a list of child game states. It fetches all the available actions for the first mover and then checks if the second mover is still alive. If the second move is still alive, this method generates all the possible actions for the second mover too. It iterates through the actions list of first and the second mover. For each combination of actions, it generates a child game state using the `createChildState` function. The resulting child game states represent different possibilities based on the actions of the movers. The list of valid child game states is returned as the output of this method. This method is used for exploring different game state scenarios by considering various actions that the mover units can take.
11. **createChildState(int id1, Action action1, int id2, Action action2):**
   The `createChildState` method generates the next child game state by performing actions on the current state. It uses a HashMap to store all the actions for both the mover one and mover two. A new game state is then created based on the current state, by performing available actions using the `performActions` method on the current state. The method returns a GameStateChild object representing the actions and the resulting child state.
12. **performActions(Map<Integer, Action> actions):**
   The `performActions` method is used to apply actions to the current game state. If the action is of type **PRIMITIVEATTACK**, it decrements the target unit's health after the attack, or if the action is of type **PRIMITIVEMOVE**, it updates the coordinates of the respective mover to reflect their new position based on the chosen direction. This method updates the game state in response to the actions taken by the game's units, including attacks and movements.
13. **fetchUnitById(int id)**:
    The `fetchUnitById` method is designed to get a game unit, be it a footman or an archer, by using its unique ID in the game. It searches for the unit within both the movers and opponents. If the unit with the specified ID is found, it is returned, else, null is returned.
14. **getActions(NextStateUnit mover, List<NextStateUnit> opponentsList):**
    The `getActions` is used to determine and collect all available actions for a specific mover, taking into account a list of opponent units. The method calculates the closest enemy unit using `getClosestUnit` method and generates an attack action if the opponent is within range using an internal state method `createPrimitiveAttack`. For a footman on maps with obstacles, it uses the A* algorithm to find the optimal path to the nearest enemy and in other cases such as if obstacles are absent or if it's the archer's turn, it generates all possible movement actions in valid directions.
10. **getDirectionWithCoordinates(NextStateUnit unit, MapLocation nextCoord):**
    The `getDirectionWithCoordinates` method is used to convert coordinates provided by the **A* pathfinding** method into the directional movement. It calculates the differences between the current unit's position and the target coordinates to determine the correct direction. Based on the differences in the X and Y coordinates, it maps the movement direction, such as North, South, East, or West. The method returns the corresponding direction to implement a movement towards the target coordinates.
11. **isOpponentInRange(NextStateUnit unit1, NextStateUnit unit2):**
     The method `isOpponentInRange` calculates the distance between two units, the player unit and the opponent unit. It then checks if the distance between these units is less than or equal to the attacking range of the player unit. Returns **true** if the opponent unit is within the attacking range of the player unit, returns **false** if its not in the range.
12. **isValidMove(int x, int y, List<NextStateUnit> gameUnits, Set<ResourceNode.ResourceView> obstacles):**
    The method `isValidMove` is used to determine if the next move is valid or not. The validity of the move is based  three conditions. Firstly, if the coordinates fall within the map extent, Secondly, if they collide with existing units and Finally, whether they occupy a tree or obstacle on the map. Returns **true** if the specified move is valid according to the conditions mentioned above, returns **false** if the conditions donot satisfy.
13. **getClosestUnit(NextStateUnit unit,List<NextStateUnit> opponents):**
  The Java method `getClosestUnit` is used to find the closest opponent in a playable unit by calculating the manhattan distance. The Manhattan distance between two points (x1, y1) and (x2, y2) is calculated as `|x1 - x2| + |y1 - y2|`. The method iterates through the list of opponents, calculates the Manhattan distance between the current unit and opponent unit, to find the closest opponent and returns the closest unit.
