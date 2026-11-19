---
sidebar_position: 4
id: piecepathcontroller
title: Robot Piece Path Controller
sidebar_label: Piece Path Controller
---

`RobotPiecePathController<TPiece, TData>` moves a held game piece through a graph of connected `GamePieceState` objects. Instead of selecting every intermediate target yourself, you send the piece to a destination and the controller follows the available route.

It wraps a node from [Robot Game Piece Controller](./GamePieceController.md), which still owns intake, physical movement, and release. The path controller handles the route between states.

## Usage

Create a `GamePieceGraph` containing the states a piece can visit. Add an edge for each allowed connection. For example, a piece can travel from the intake to stow, then from stow to the placing position:

```cs
var graph = new GamePieceGraph()
    .AddNodes(coralIntakeState, coralStowState, coralPlaceState)
    .AddEdge(coralIntakeState, coralStowState)
    .AddEdge(coralStowState, coralPlaceState);
```

Connections work in both directions. With this graph, sending a piece from intake to place takes it through stow. The graph chooses the route with the fewest connections, rather than comparing travel distances or movement speeds.

Use the same state objects in the graph, the underlying node, and calls such as `SendTo`. Graph membership and arrival checks compare the objects themselves; a new state with the same name does not count as the same graph node. Give each state a unique positive `stateNum`, a unique name within the underlying node, and a valid target Transform.

In a [Command Robots](./CommandRobots.md) robot derived from `ReefscapeCommandRobotBase`, `CoralPathController` and `AlgaePathController` are already created by the base class. Once those references exist, configure the appropriate node and graph:

```cs
var coralNode = RobotGamePieceController.GetPieceByName("Coral");
if (coralNode == null)
{
    Debug.LogError("The robot needs a Coral game piece node.");
    return;
}

coralNode.gamePieceStates = graph.GetStates();
coralNode.intakes.Add(coralIntake);
CoralPathController.SetGraph(graph);
```

These examples are fragments inside a Reefscape robot class; the state and intake variables are configured fields. Assign the underlying node's states before `RobotGamePieceController.Start()` builds its lookup. If the intake is already assigned in the Inspector, do not add it again.

For a robot without the command base's existing wrapper, create one around the node:

```cs
var pathController = new ReefscapeRobotPiecePathController(coralNode);
pathController.SetGraph(graph);
```

This is a regular C# object, so it needs `Tick()` calls to advance its route. Call it once per physics update. `ReefscapeCommandRobotBase` already ticks its two path controllers, so subclasses should preserve that base update instead of adding another tick.

From your game piece update logic, request intake and send the acquired piece to a destination:

```cs
CoralPathController.RequestIntake(
    coralIntake, IntakeAction.IsPressed());
CoralPathController.SendTo(coralStowState);
```

Keep supplying the current intake input, including `false` when released. `RequestIntake` also synchronizes the path controller's current node once the underlying node owns a piece and is at its target while path traversal is idle. `SendTo` does nothing while empty, so keep requesting the desired destination as intake completes. Repeating the same destination is supported.

When placing, check both possession and arrival before releasing:

```cs
CoralPathController.SendTo(coralPlaceState);

if (CoralPathController.HasPiece() &&
    CoralPathController.IsAt(coralPlaceState))
{
    CoralPathController.Release(transform.forward * 2f);
}
```

`Release` delegates to the underlying node's impulse release with its default arrival guard. It returns `MoSimResult<MoSimLib.Void>`, not a boolean indicating whether the piece was released. It also clears the path and resets traversal even if the underlying release did not happen, so keep release calls gated by arrival.

## Advanced Usage

Use `.OnGamePiece()` callbacks to choose destinations for robot states:

```cs
InState<StowState>()
    .OnGamePiece(() => CoralPathController.SendTo(coralStowState));

InState<IntakeState>()
    .OnGamePiece(() =>
    {
        CoralPathController.RequestIntake(
            coralIntake, IntakeAction.IsPressed());
        CoralPathController.SendTo(coralStowState);
    });
```

If `SendTo` receives a different destination during a hop, it stores the request until the piece reaches the next node, then calculates a route from there. Further changes replace the pending request. Repeating the already active destination returns immediately and does not cancel a pending change.

The query methods serve different purposes:

| Method | What it checks |
| --- | --- |
| `HasPiece()` | Whether the underlying node owns a piece. |
| `IsAt(state)` | Whether the tracked current node is that state and traversal is not moving. |
| `IsAtDestination()` | Whether a destination exists and `IsAt(destination)` is true. |

The arrival queries do not check possession. Tracking can remain after release or while empty, so combine them with `HasPiece()` when making decisions about a held piece. These queries return `MoSimResult<bool>`, which implicitly converts to `bool`; `.Value` also accesses the payload.

For an intake handoff that needs a specific entry state, use:

```cs
CoralPathController.RequestIntakeFromSmoothHandoff(
    coralIntake,
    coralIntakeState,
    IntakeAction.IsPressed());
CoralPathController.SendTo(coralStowState);
```

While empty, requesting intake through this method selects `coralIntakeState` on the underlying node. After acquisition, it synchronizes tracking in the same way as `RequestIntake`. It does not enable `smoothHandoff` itself; configure that behavior on the intake and states as needed.

For a piece that starts at a known preload position, `SetGraph(graph, coralStowState)` sets both the tracked current node and destination to stow and marks traversal arrived. It does not move or spawn a piece. Configure the actual preload separately through `RobotGamePieceController.SetPreload(coralStowState)` before that controller's startup.

The constructor taking `(graph, node)` instead initializes the tracked current node from the graph's preload marker, registered with `.AddNode(coralStowState, isPreload: true)`. The one-argument `SetGraph(graph)` only replaces the graph; it does not initialize the current node or clear an active route. Configure graphs during setup, and use the explicit-node overload only when the tracked position matches the actual piece position.

If a state is missing from the graph or no connected route exists, pathfinding logs an error and leaves the previous destination and route unchanged. The `SendTo` result wrapper still reports success, so it is not an arrival or route-validation check.
