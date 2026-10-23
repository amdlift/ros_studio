# The backend ships a full ROS 2 Jazzy installation

The FastAPI service is built `FROM ros:jazzy`, taking the image from roughly 150 MB to over 1 GB. In exchange the installation is queried directly for two things that are otherwise guesswork.

The **Catalog** — every interface type offered in the field and Hub type pickers — comes from `ros2 interface list` and `ros2 interface show`, cached at startup, instead of a hand-curated list of `std_msgs`/`geometry_msgs`/`sensor_msgs` types that silently rots as distributions move.

**Verify** runs an actual `colcon build` of a generated Workspace in a temp directory, which is the only way to know the Scaffold promise holds rather than hope it does.

## Consequences

- Verify is an explicit, opt-in action with a hard timeout and a small concurrency cap. It is never on the download path: the zip must always be one fast request away.
- Verify reports pass/fail per Package plus the raw log, with no source-line mapping. Packages are Graph entities and `colcon` already reports per package, so attribution is nearly free; generated line numbers map back to nothing a user can act on, because users do not write code in this tool.
- A Verify failure indicates a generator bug, a bogus entry in a Package's extra-dependencies list, or a Catalog type whose owning package was not declared — not user error.
- The tool is pinned to one distribution. Supporting a second means a second image or a multi-distro scheme.
