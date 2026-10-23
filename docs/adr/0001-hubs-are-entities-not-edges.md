# Hubs are first-class entities, not edges

A ROS 2 topic is not a declared thing — it is an implicit rendezvous where a publisher and a subscriber happen to agree on both name and type. Modelling that as an edge between two Nodes means a topic with one publisher and three subscribers is three edges each independently carrying the same name and type, so renaming it requires editing all three and getting it wrong silently splits the graph into two topics. We model the rendezvous itself as a **Hub**: a canvas node holding the name and Interface in one place, with typeless edges carrying only direction.

## Consequences

- Services fall out of the same model as a Hub constrained to exactly one server, and actions can later be added as a third Hub kind rather than a new concept.
- Hubs are inherently cross-Package, so they sit on the root canvas outside the Package bubbles rather than inside them.
- A Hub can exist with no Interface assigned (an Untyped Hub), which is why drag-to-connect can create one before the user has chosen a type.
