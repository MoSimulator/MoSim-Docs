---
sidebar_position: 3
id: animsys
title: Game Piece Animation System
sidebar_label: Game Piece System
---

# Animation System

Our game piece animation system is made up of a handful of different parts: `GamePieceState`, `GamePieceIntake`, `GamePieceGraph`, and `RobotPiecePathController`. 

### Nodes

These are the core of the system, each node represents a target state for a piece to occupy or pass through. Each state is declared via 
```cs
[SerializeField] private pieceState;
```
Each state is set up in the inspector and handled via the following settings.

<img src="/img/mason/animations/piece.png" alt="Game Piece" width="50%"/>


`Name` is the lookup key used when selecting a state by name. Use a lowercase copy of the field name as a convention. Matching ignores capitalization and leading/trailing whitespace, but spaces inside the name still matter. Keep names unique within the same piece system. 

`State Num`, use a positive number unique within the same piece system; 0 represents no held piece.

`State Move Speed` is the speed in inches/second when moving TO the state. A value of 0 completes the move immediately; with `Smooth Handoff` disabled, the piece snaps to the target on the next controlled update. `Angular Speed` controls rotation in degrees/second; 0 resets local rotation immediately during a nonzero-speed move.

`State Target` is the Transform on the robot that supplies the target position and rotation.

`Smooth Handoff` keeps the piece in its moving mode after arrival, skipping the normal final position and rotation snap to the target. Use it for transitions that immediately continue to another state, rather than a final holding state. It does not disable rotation during via move speed during the move.

## Game Piece Intakes

Refer to [Animation Intakes](./AnimationIntakes.md) for the setup process, can be added to the code with 
```cs
[Header("Intake Components")] [SerializeField] private ReefscapeGamePieceIntake pieceIntake;
```

## RobotPiecePathController.

The path controller routes a piece from node to node; `RobotGamePieceController` and the piece controller perform the actual movement. For Reefscape, the path controller is `ReefscapeRobotPiecePathController`. You start by assigning the graph nodes for each piece type. `GamePieceGraph` defines the allowed routes within the robot. Add each state with `AddNode`, then connect states with `AddEdge`; each edge allows travel in both directions. Mark the preload state with `isPreload: true`, and call `SetPreload` separately to register the actual preload. `SetGraph(graph, startingState)` initializes the path controller's tracked starting state; it does not move or spawn a piece.

```cs
private void SetupGamePieceController()
  {
    var coralGraph = new GamePieceGraph()
     .AddNode(coralStowState, isPreload: true)
     .AddNode(pieceState)
     .AddEdge(coralStowState, pieceState);
    
    // Register states and intakes before RobotGamePieceController.Start() runs
    _coralController = RobotGamePieceController.GetPieceByName(ReefscapeGamePieceType.Coral.ToString());
    _coralController.gamePieceStates = coralGraph.GetStates();
    _coralController.intakes.Add(coralIntake);
    
    // Register the preload state; the piece is created during controller startup
    RobotGamePieceController.SetPreload(coralStowState);
            
    // Initialize the existing path controller graph and tracked starting state
    CoralPathController.SetGraph(coralGraph, coralStowState);
  }
```

`SendTo(node)` requests a destination through connected graph nodes from the path controller's tracked current node. It requires a held piece and a valid graph route; it cannot recover an arbitrary untracked state. A destination change during a hop takes effect at the next node arrival. Release pieces through the piece controller node using `ReleaseGamePieceWithForce(force)` or `ReleaseGamePieceWithContinuedForce(force, time, maxSpeed)`. Both default to releasing only when `atTarget` is true; pass `whenAtTarget: false` for immediate release. The force vector uses the piece controller's local axes.

```cs
private void HandlePiece() {
    if (Outtake) {
        _coralController.ReleaseGamePieceWithForce(new Vector3(0, 0, 6));
    } else {
        CoralPathController.SendTo(coralStowState);
    }
}
```
