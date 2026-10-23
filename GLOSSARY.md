# ros_studio

A web tool for designing a ROS 2 Jazzy workspace graphically and generating it as a downloadable source workspace.

## Language

### The design

**Graph**:
The complete design the user is authoring: every Package, Node, Interface, and Hub, plus the edges between them. The single source of truth from which a Workspace is generated.
_Avoid_: project, diagram, document, scene

**Canvas node**:
A React Flow rendering primitive. Only ever used to talk about the frontend's drawing layer, never about ROS 2 concepts.
_Avoid_: node (unqualified)

### ROS 2 concepts

**Package**:
A unit of the generated Workspace that becomes one directory with its own `package.xml`. Every Package has exactly one Package kind.
_Avoid_: module, bundle, library

**Package kind**:
One of Interface, Python, C++, or Bringup. Determines the Package's build type and what it may contain; the user chooses a kind and never a build type directly.
_Avoid_: package type, build type (as a user-facing choice)

**Bringup Package**:
The single derived Package emitted with every Workspace, holding the one launch file that starts every Node in the Graph. Never created or edited by the user.
_Avoid_: launch package, main package

**Node**:
A single ROS 2 node — one executable with a node name, living inside a Python or C++ Package. Never appears in an Interface Package.
_Avoid_: process, component, canvas node

**Interface**:
A custom message or service definition. Lives only in an Interface Package, which is the sole place `rosidl` generation happens.
_Avoid_: type, schema, contract

**Hub**:
A named rendezvous point that Nodes attach to — currently a Topic or a Service. Carries the name and Interface in one place; the edges to it are typeless and carry only direction. Hubs are cross-Package and so sit outside Package bubbles on the canvas.
_Avoid_: channel, connection, link, edge

**Topic**:
A Hub with any number of publishers and any number of subscribers.

**Service**:
A Hub with exactly one server and any number of clients.

**Parameter**:
A declared ROS 2 parameter belonging to a single Node, with a name, type, and default value. Its type comes from the nine-member ROS 2 parameter type set, which is not the type system Interface fields use.

**Untyped Hub**:
A Hub with no Interface assigned yet — the state a Hub is born in when drag-to-connect creates it, and the state its Hubs fall back to when an Interface they used is deleted. Legal to have on the canvas; blocks generation.

### Correctness

**Prevented**:
A state the editor makes impossible to reach, because it could never generate a valid Workspace — a Node in an Interface Package, a second server on a Service, an Interface cycle.

**Error**:
A state the Graph is allowed to hold while the user works, but which blocks generation — most commonly an Untyped Hub.

**Warning**:
An incomplete-looking state that is surfaced but blocks nothing, such as a Topic with no subscribers or an unused Interface.

### Tooling

**Catalog**:
The set of interface types a field or Hub may refer to: every type reported by the backend's ROS 2 Jazzy installation, plus the Graph's own Interfaces.
_Avoid_: type list, registry, index

**Verify**:
An opt-in, server-side `colcon build` of a generated Workspace, used to confirm it compiles. Never part of the download path.
_Avoid_: validate, check, compile

### Output

**Workspace**:
The generated source tree — the `src/` directory of Packages produced from a Graph, delivered as a zip. Always generated fresh; never read back in.
_Avoid_: output, bundle, export, project

**Layout**:
The canvas positions of a Graph's entities, stored alongside the Graph but never read by generation — two Graphs differing only in Layout generate identical Workspaces.
_Avoid_: positions, view, viewport

**Scaffold**:
The quality bar for generated code: it builds under `colcon build` and runs under the generated launch file, with user logic confined to marked `TODO` regions inside working callbacks.
_Avoid_: stub, skeleton, boilerplate
