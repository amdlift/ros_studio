# Build type derives from Package kind; interfaces are isolated

The ROS 2 Jazzy tutorial on custom interfaces states that an interfaces package "is, and can only be, an ament_cmake package," and creates custom `.msg`/`.srv` files "in their own package" to be used from a separate one. Rather than exposing `ament_python` vs `ament_cmake` as a user choice and then validating the combinations, we expose three Package kinds — Interface, Python, C++ — and derive the build type from the kind.

## Consequences

- "A custom message in a Python package" is structurally unreachable rather than a validation error.
- A Package that mixes rosidl generation with its own nodes cannot be expressed. This is deliberate; it is also the arrangement ROS 2 documentation steers away from.
- A fourth kind, Bringup, is derived rather than user-created: one per Workspace, holding the single launch file that starts the whole Graph.

## Considered options

Letting the user pick a build type directly, with validation rejecting interfaces in `ament_python` packages. Rejected because it presents a choice whose only correct answers are already determined by what the Package contains.
