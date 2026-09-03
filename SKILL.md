---
name: rebuild-miniprogram
description: Rebuild a WeChat Mini Program after any source change and verify the live result in WeChat DevTools. Use whenever the user asks to recompile, refresh, reopen, re-import, rebuild, preview, or verify the mini program — including casual asks like "刷新一下小程序" or "看下最新效果" after code changes, or when DevTools seems to show stale output. Runs the real build, fully restarts DevTools, imports only the generated mp-weixin directory, compiles, and verifies. Works for uni-app and any project that produces an mp-weixin build.
---

# Rebuild Mini Program

Run the complete rebuild-and-refresh flow. Do not treat hot reload, an old build directory, or a zero-exit build command alone as acceptance — DevTools happily keeps serving a stale compiled artifact that looks current, and debugging against it wastes hours.

## Locate the build

Detect the build command and artifact from the project instead of assuming fixed values:

1. Find the directory holding the mini-program's `package.json` (often `apps/miniprogram` in a monorepo, or the repo root).
2. From its `scripts`, find the WeChat build script — usually `build:mp-weixin`. If absent, look for any `build:mp*` script and confirm with the user before running.
3. Run the build from that directory, with the package manager the project already uses (match the lockfile).
4. Record the artifact path — normally `<package-dir>/dist/build/mp-weixin` for uni-app — and verify it contains `app.json` and `project.config.json` before opening DevTools. A directory missing either file is not an importable mini program.

## Prepare WeChat DevTools

Use Computer Use for WeChat DevTools, targeting controls by app, window, role, and visible label rather than screen coordinates.

1. With a project open, clear its caches with the visible cache controls. Clear `file` and `compile` only: those hold the compiled state that goes stale. Do not clear `storage`, `auth`, or `all` — they erase login and test state, so the refresh would destroy the very state you are trying to verify.
2. Return to the launcher and open `管理`. If an old `mp-weixin` recent-project entry exists, remove only that launcher entry — never the repository or generated files on disk.
3. Fully quit DevTools with Command-Q or its application menu and confirm the app exited. Closing only the project window leaves toolchain processes running, which is exactly how stale artifacts survive a "restart".

## Build

1. Do not read or print `.env` contents.
2. Run the build command recorded above. Require exit code 0 and a successful build message.
3. Confirm `app.json` and `project.config.json` exist in the artifact. If the build fails, stop and report the error; never open stale output as if it were current.
4. If the project defines separate local and release build variants (e.g. `build:mp-weixin` plus `build:mp-weixin:release` taking the API base URL from an env var), both usually write the same `dist/build/mp-weixin` directory. Pick the variant that matches the user's intent. A local build silently overwrites a release artifact — build logs and size checks look identical — so after producing a release package, never run the local variant (including DevTools-triggered recompiles) before it is uploaded.
5. When the build embeds an API base URL at compile time, verify the artifact actually contains the intended URL before reporting success: grep the generated `dist` for the expected domain and confirm no development/loopback URL remains (e.g. `grep -rl "<domain>" dist/build/mp-weixin` must match; `grep -rl "127.0.0.1" dist` should be empty unless the local variant was intended).

## Relaunch and import

1. Relaunch DevTools from the Dock or Applications and choose `导入`.
2. In the file chooser, use Go to Folder when available and select exactly the generated `mp-weixin` directory. Importing the repo root, `apps`, `dist`, or `build` makes DevTools compile the wrong tree, and the mistake only surfaces later as confusing module errors.
3. Before creating the project, verify the chosen folder or proposed project name ends in `mp-weixin`. Select the test mini-program identity already configured in DevTools without copying, logging, or hard-coding its AppID.
4. Activate `创建`, then `信任并运行` if prompted.

## Compile and verify

1. Verify the window title begins with `mp-weixin` rather than a parent directory name — the title is the fastest signal of what was actually imported.
2. Select the `普通编译` mode and activate `编译` if compilation does not start automatically.
3. Wait for compilation to finish, then re-read the current UI state.

Require all four before reporting success:

- no visible compile error or failure state;
- the simulator renders the expected current Mini Program page;
- the project remains identified as `mp-weixin`;
- the DevTools window remains open for the user.

If the wrong directory was imported, close the incorrect project, remove only its launcher entry, and retry with the exact generated directory. If compilation fails, preserve the error evidence and stop without claiming success.
