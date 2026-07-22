# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

> **Fork maintained by [k3-2o](https://github.com/k3-2o).**  
> Upstream: [mikeyobrien/pi-provider-kiro](https://github.com/mikeyobrien/pi-provider-kiro).  
> This fork is independently maintained and may diverge from upstream.  
> Upstream changes are periodically merged when relevant.

## [0.8.2-fix.2] - Unreleased

### Fixed (fork)

- **Spurious "maximum output token limit" error on every completion**: The `stopReason` was incorrectly set to `"length"` whenever the Kiro API did not send a `contextUsage` event in the stream. Kiro does not reliably emit this event — its absence is not evidence of truncation. Now defaults to `"stop"` (normal completion) unless tool calls were emitted (`"toolUse"`).
- **`k3-2o/pi-provider-kiro:dist` extension name**: `pi.extensions` pointed to `./dist/index.js`, causing pi to append `:dist` when installed via git. Added a root `index.ts` wrapper so it shows cleanly as `k3-2o/pi-provider-kiro`.

### Fixed (merged from upstream)

- **profileArn 403 spam on AWS Builder ID (upstream issue [#81](https://github.com/mikeyobrien/pi-provider-kiro/issues/81))**: `ListAvailableProfiles` returns 403 Forbidden for every AWS Builder ID account because Builder ID has no profile concept. Previously `resolveProfileArn()` warned and returned without caching, so it re-hit the endpoint on every single message — producing a persistent `[pi-provider-kiro] Failed to resolve profileArn...` warning in pi's prompt box and a duplicate "Working..." spinner. Failures are now held in a negative cache for 10 minutes: the first failure warns once with a clearer message, subsequent attempts are skipped silently via `debugLog`, and one retry is allowed after cooldown. `profileArn` remains optional downstream, so generation was never affected — this is purely a UX fix.
- **Tool schema 400 `TOOL_SCHEMA_INVALID` on Bedrock**: `convertToolsToKiro()` passed each tool's `parameters` through raw, so any tool whose typebox schema omitted `type` or used a non-object root caused Kiro to reject the entire request. Generation could not start at all while such a tool was in the set. The new `normalizeToolInputSchema()` coerces every schema to `type: "object"` and defaults `properties` to `{}`.

## [0.8.2-fix.1]

### Fixed (fork)

- **profileArn 403 spam on AWS Builder ID (upstream issue [#81](https://github.com/mikeyobrien/pi-provider-kiro/issues/81))**: Same as above — first fork release shipping the merged fix ahead of upstream release cadence.

## 0.8.0 — 0.1.1

> These versions are from the upstream repository. See [mikeyobrien/pi-provider-kiro](https://github.com/mikeyobrien/pi-provider-kiro) for original changelog.

[0.8.2-fix.1]: https://github.com/k3-2o/pi-provider-kiro/releases/tag/v0.8.2-fix.1
[Unreleased]: https://github.com/k3-2o/pi-provider-kiro/compare/v0.8.2-fix.1...HEAD
