# Generation is one-way; ROS 2 source is never parsed

The Graph is the only source of truth, and a Workspace is always generated fresh from it. The tool never reads a `package.xml`, `CMakeLists.txt`, `setup.py`, or node source back in. Round-tripping would mean parsing three build systems plus source-level analysis to recover publisher and subscriber calls, and a merge strategy for hand-edited code — more work than the generator itself, and the correctness bar is far higher.

## Consequences

- Existing ROS 2 workspaces cannot be imported. The tool bootstraps new ones.
- Regenerating overwrites user code written in the `TODO` regions. We generate one file per Node anyway, matching the shape of the ROS 2 tutorials, and state the overwrite loudly in the UI and the generated README.
- Interfaces are authored in a structured field editor with a read-only IDL preview, not a raw text box — accepting raw IDL would mean parsing IDL, which contradicts this decision.

## The upgrade path, if round-tripping is ever needed

Split each Node into a fully generated base class marked do-not-edit plus a thin subclass holding the `TODO` regions, so regeneration overwrites only the base. Rejected for now because it costs every Node an extra file and an inheritance hop, which works against output that should look like the tutorials a beginner just read.
