# The backend is stateless; the Graph is a client-owned versioned document

The Graph lives in the browser. The backend is a pure function — `POST` a Graph, receive a zip — with no database, no authentication, and no server-side project entity. Persistence is local autosave plus explicit save/load of a Graph file.

## Consequences

- No database in `docker-compose.yml`, no migrations, no auth, no multi-user concerns.
- The Graph carries a `schemaVersion` from the first commit, with a version check and a migration registry that starts empty. Documents from unknown older versions are rejected with a clear message; documents from a newer version than the backend knows are rejected rather than best-effort parsed, since that is the case that silently drops data.
- Canvas positions live in a separate `layout` section of the same document, which generation ignores entirely. Two Graphs differing only in layout must generate byte-identical Workspaces — worth an explicit test.
- Undo/redo is a bounded stack of whole-document snapshots rather than a command/inverse-command system, because the document is a plain serialisable value.
- Adding server-side projects later needs no change to the generator, provided the Graph stays a self-contained serialisable value.
