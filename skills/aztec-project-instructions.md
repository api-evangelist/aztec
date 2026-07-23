---
name: Aztec project instructions (CLAUDE.md / AGENTS.md)
description: Aztec's own recommended project-level AI instruction file for Aztec smart-contract and Aztec.js development — the critical gotchas that keep an agent from producing broken artifacts.
api: none (SDK / smart-contract framework — no OpenAPI)
method: searched
source: https://docs.aztec.network/developers/ai_tooling
operations: []
---

# Aztec project instructions (provider-published)

Aztec publishes a recommended `CLAUDE.md` / `AGENTS.md` on its AI Tooling page for any
project building Aztec smart contracts or Aztec.js applications. The block below is
copied verbatim from https://docs.aztec.network/developers/ai_tooling. For Claude Code,
place it in `CLAUDE.md`; for Codex, in `AGENTS.md`. Aztec also ships an official MCP
server (`@aztec/mcp-server`, see `../mcp/aztec-mcp.yml`) that provides on-demand code,
docs, and example search.

```
# Aztec Project

## Critical: Use the `aztec` CLI, not `nargo` or `bb` directly

This is an Aztec smart contract project. Always use the `aztec` CLI wrapper instead of calling `nargo` or `bb` (the Barretenberg prover) directly:

- **Compile**: `aztec compile` (NOT `nargo compile`). Using `nargo compile` alone produces incomplete artifacts.
- **Test**: `aztec test` (NOT `nargo test`).
- **Prove**: NEVER call `bb` directly. Proof generation is handled for you by the PXE through the `aztec` CLI and `aztec.js`. There is no contract-development workflow that runs `bb` by hand.
- **Other nargo commands** like `aztec-nargo fmt` and `aztec-nargo doc` are fine to use directly. The Aztec installer exposes the bundled `nargo` as `aztec-nargo`; bare `nargo` resolves to your own install (if any), not the bundled one.

## Error Handling

- NEVER silently swallow errors or fall back to default values. If a value is required, throw if it's missing.
- NEVER use fallback values like `AztecAddress.ZERO`, `"unknown"`, `0`, or `null` to mask missing data. These hide bugs and cause failures elsewhere that are harder to trace.
- NEVER add retry/polling logic unless explicitly asked. Retry loops with long timeouts may brick application loops and mask the real error.
- NEVER wrap calls in try/catch that returns null or a default. Let errors propagate.
- If a precondition isn't met, throw immediately with a descriptive message — don't try to "work around" it.
- Prefer `T` return types over `T | null` when null would indicate a bug rather than a valid state.
- Do not add `.catch(() => defaultValue)` to promises. If something fails, the caller needs to know.

## Version Compatibility

The Aztec developer SDK/aztec-nr version (used for writing and compiling contracts) may differ from the node version (used by operators to run the network). Check the [Networks page](https://docs.aztec.network/networks) for current network versions. When in doubt, use the version from the developer docs you are reading, it is the correct SDK version for contract development on that network.

## Hashing: Default to Poseidon2

When writing Aztec.nr contract code that requires hashing, **always use Poseidon2** unless a specific protocol or interoperability requirement calls for a different hash.

- **Default**: `use aztec::protocol::hash::poseidon2_hash;`
- **Do NOT** default to Pedersen (`pedersen_hash`). Pedersen is available but Poseidon2 is cheaper in circuits and is the standard across Aztec.
- If you are unsure which hash to use, use Poseidon2.
```

## Related official tooling

- **MCP servers** — `claude mcp add aztec -- npx @aztec/mcp-server@latest` (Aztec) and
  `claude mcp add noir -- npx noir-mcp-server@latest` (Noir, community). See
  https://docs.aztec.network/developers/ai_tooling.
- **Community skills** — `aztec-skills` (NethermindEth/aztec-skills) provides installable
  Claude Code / Codex skills for Aztec contracts, deployment, Aztec.js, and testing.
