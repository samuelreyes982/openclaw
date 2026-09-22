# OpenClaw · Open-source contribution

**Samuel Reyes — recovering missing local inference executables**

This is my contribution fork of [OpenClaw](https://github.com/openclaw/openclaw), an open-source AI assistant maintained by the OpenClaw Foundation and its contributors. My work focuses on a reliability fix for the llama.cpp plugin.

[View pull request #153523](https://github.com/openclaw/openclaw/pull/153523) · [Read the code changes](https://github.com/openclaw/openclaw/pull/153523/files) · [Browse the contribution branch](https://github.com/samuelreyes982/openclaw/tree/fix/llama-managed-server-recovery)

## The problem

Local chat and embeddings could fail with `spawn ENOENT` when the configured, OpenClaw-managed `llama-server` executable disappeared—even when the model remained cached. Users had to repair the installation or repeat setup.

## My contribution

- Added recovery during shared server preparation, reusing OpenClaw's existing verified installer.
- Matched the configured executable to the current host's pinned managed installation, preserving its selected CPU or GPU backend.
- Kept existing executables and custom paths unchanged, and surfaced filesystem errors other than a missing file.
- Added 10 regression cases covering chat and embedding preparation, recovery boundaries, backend matching, and failure handling; documented the behavior.

**Technologies:** TypeScript, Node.js, Vitest, llama.cpp, local inference, HTTP embeddings.

## Validation

The [PR evidence](https://github.com/openclaw/openclaw/pull/153523) records a real before-and-after reproduction on macOS arm64 with Metal, using the actual installer, cached model, server process, embedding provider, and HTTP transport.

| Check | Before the fix | With the fix |
| --- | --- | --- |
| Embedding request after removing the managed executable | `spawn ENOENT` | Succeeds |
| Executable recovery | Remains missing | Verified executable restored |
| Embedding output | None | 768 finite values |
| Direct embedding HTTP request | Not reached | HTTP 200 |
| Process cleanup and port closure | Confirmed | Confirmed |

The PR records **281 passing tests across 16 files**, including the 10 recovery cases, plus passing production and test type checks. The [GitHub CI run](https://github.com/openclaw/openclaw/actions/runs/35506799849) passed its CI gate at contribution commit `f4b09fac5f6b`.

Live inference evidence covers macOS arm64 Metal embeddings. Chat preparation and other backend selection paths have automated regression coverage; live chat, Linux, and Windows/CUDA inference were not validated by this reproduction. Custom paths, older releases, and installations for other hosts or state directories require manual repair or setup.

## Contribution status

**Submitted upstream; open and unmerged as of September 22, 2026.** The [pull request](https://github.com/openclaw/openclaw/pull/153523) is the source of truth for review, CI, and merge status.

## Upstream project

For installation and general product usage, use the [official OpenClaw documentation](https://docs.openclaw.ai/) and [upstream README](https://github.com/openclaw/openclaw/blob/main/README.md). This fork highlights my contribution and does not represent an official OpenClaw release.

OpenClaw is licensed under the [MIT License](LICENSE), copyright the OpenClaw Foundation. Upstream authorship and third-party notices remain with their respective contributors.
