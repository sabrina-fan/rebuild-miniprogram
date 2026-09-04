# rebuild-miniprogram

Rebuild a WeChat Mini Program after any source change and verify the live result in WeChat DevTools — not just run a build command and assume it worked.

## Why

DevTools happily keeps serving a stale compiled artifact that *looks* current. Hot reload can silently fail, and a zero-exit build doesn't guarantee the simulator picked up the new output. Debugging against a stale build wastes hours.

This skill runs the real build, fully restarts DevTools, imports only the generated `mp-weixin` directory, compiles, and verifies the result is actually live.

## Install

### Option A — let your agent install it

Give your agent this repo URL and ask it to add the skill:

```
https://github.com/sabrina-fan/rebuild-miniprogram
```

### Option B — manual

Copy the `rebuild-miniprogram/` directory into your agent's skills folder (e.g. `~/.zcode/skills/`).

## Requirements

- WeChat DevTools installed and accessible.
- A project that produces an `mp-weixin` build (uni-app or any compatible framework).
- Computer Use capability for driving the DevTools GUI (the skill targets controls by app/window/label, not screen coordinates).

## Usage

Trigger it naturally — "刷新一下小程序", "看下最新效果", "rebuild the mini program", or any time DevTools might be showing stale output.

The skill will:

1. Detect the build command and artifact path from `package.json`.
2. Run the build and confirm the artifact has `app.json` + `project.config.json`.
3. Quit DevTools completely, clear stale caches, and re-import only the generated `mp-weixin` directory.
4. Compile and verify four success conditions: no compile error, expected page renders, project identified as `mp-weixin`, window stays open.

## Compatibility

- **macOS** — primary platform (uses Computer Use for DevTools GUI automation).
- **Windows / Linux** — should work if your agent has equivalent GUI automation capability, but not formally tested.

## Security & Boundary

This skill handles **rebuild + verify**. It does not write or modify source code, debug business logic, or run unit tests.
