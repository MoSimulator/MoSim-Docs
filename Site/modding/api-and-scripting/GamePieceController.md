---
sidebar_position: 3
id: gamepiececontroller
title: Robot Game Piece Controller
sidebar_label: Game Piece Controller
---

`RobotGamePieceController<TPiece, TData>` manages the game pieces held by a robot. It connects the intakes to named piece states so robot code can request intake, move a piece between locations, and release it. Each game provides a concrete controller, such as `ReefscapeRobotGamePieceController`.

## Usage

The controller contains a list of `gamePieceNodes`. Each node is one slot for a held piece, with a `pieceName`, a list of `intakes`, and an array of `gamePieceStates`. A Reefscape robot will commonly have separate Coral and Algae nodes.

Add the nodes to the game-specific controller in the Inspector. Each node needs an intake and its available states. A `GamePieceState` defines where the piece should go:

| Field | Usage |
| --- | --- |
| `name` | The name used by `SetTargetState`. Use a unique, lowercase name within the node. |
| `stateNum` | A unique positive number within the node. `0` is reserved for an empty node. |
| `stateTarget` | The Transform the piece moves to and attaches to. |
| `stateMoveSpeed` | The movement speed, documented in inches per second, 0 causing teleportation. |
| `angularSpeed` | The rotation speed, documented in degrees per second, 0 causing teleportation. |
| `smoothHandoff` | Preserves movement through a handoff. Use `false` for a final holding position. |

You can also assign states and intakes from robot code. Do this before the game piece controller's `Start()`, which builds its state lookup and resets the nodes. Changing the state array after that does not rebuild the lookup.

The examples below are fragments inside a Reefscape robot class. `RobotGamePieceController` is its game-specific controller reference; the state, intake, and Transform variables are fields configured on the robot.

```cs
var coralController = RobotGamePieceController.GetPieceByName("Coral");
if (coralController == null)
{
    Debug.LogError("The robot needs a Coral game piece node.");
    return;
}

coralController.gamePieceStates = new[]
{
    coralIntakeState, coralStowState, coralPlaceState
};
coralController.intakes.Add(coralIntake);
```

`GetPieceByName` ignores capitalization and surrounding spaces, and returns `null` if the node cannot be found. The node's `pieceName` must match the piece name expected by its intake. If the intake is already assigned in the Inspector, there is no need to add it again in code.

Once configured, use the node from your game piece update logic:

```cs
// Intake: keep supplying the current input, including false when released.
coralController.SetTargetState(coralIntakeState);
coralController.RequestIntake(coralIntake, IntakeAction.IsPressed());
```

The node only accepts intake requests while empty. Once an intake secures a piece, the node takes ownership and moves it toward the requested state. If no target was requested, it uses the first state in `gamePieceStates`.

```cs
// Stow: move the held piece to its holding position.
coralController.SetTargetState(coralStowState);

// Check whether the node owns a piece.
bool hasCoral = coralController.HasPiece();
```

`SetTargetState` accepts either a `GamePieceState` or its name. Passing a state object looks up its `name`; that state must already be registered on the node. The call requests movement, and `FixedUpdate` performs it. `HasPiece()` checks whether `currentStateNum` is greater than `0`; it does not mean the piece has reached its target.

```cs
// Place: call this from the placing update until the release succeeds.
coralController.SetTargetState(coralPlaceState);

if (coralController.currentStateNum == coralPlaceState.stateNum &&
    coralController.atTarget)
{
    bool released = coralController.ReleaseGamePieceWithForce(
        transform.forward * 2f);
}
```

Release defaults to `ForceMode.Impulse` and `whenAtTarget: true`. It returns `false` if there is no controlled piece or the target has not been reached. A successful release clears the piece controller, sets `currentStateNum` to `0`, and clears `atTarget`.

## Advanced Usage

If a new target is requested while a piece is moving, the controller finishes the current movement first. It then starts toward the latest requested target. `movingTo` holds the requested target name; `wasMovingTo` identifies the movement being processed. `GetCurrentState()` resolves `wasMovingTo`, so use `atTarget` as well when checking arrival. This is why the placing example checks both the state number and `atTarget`.

For an immediate outtake, bypass the arrival check. For a powered release, use continued force:

```cs
coralController.ReleaseGamePieceWithForce(
    transform.forward * 2f,
    whenAtTarget: false);

// Alternative release: apply force over time with a speed limit.
coralController.ReleaseGamePieceWithContinuedForce(
    transform.forward * 2f,
    time: 0.2f,
    maxSpeed: 5f);
```

These are alternative release methods. Continued force runs a coroutine through the robot game piece controller, and the node becomes empty as soon as the release starts.

`MoveIntake` changes an intake's capture target without changing the node's states. `IntakeHasPieces` checks the intake's `hasGamePiece` flag, which is separate from the node owning a secured piece.

```cs
coralController.MoveIntake(coralIntake, alternateIntakeTarget);
bool pieceInIntake = coralController.IntakeHasPieces(coralIntake);
```

To configure a preload, call `SetPreload(coralStowState)` before the controller's `Start()`. It uses the `RobotBase.PreloadGamePiece` prefab on the same GameObject, spawns it at the preload target, and assigns it to the first node without a controller. Put the intended preload node first and include the preload state in that node's states. Calling `SetPreload` after startup does not spawn another piece.

Most operations return `MoSimResult<T>`. Its implicit conversion allows the direct boolean assignments shown above; `.Value` also accesses the payload. For release methods, that boolean tells you whether a release started. Invalid target names log an error and leave the target unchanged, even though `SetTargetState` returns a successful result wrapper.
