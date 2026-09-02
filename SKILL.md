---
name: rebuild-miniprogram
description: Rebuild and refresh a WeChat Mini Program after any source change, or when the user asks to recompile, refresh, reopen, import, or verify the Mini Program in WeChat DevTools. Run the real build, fully restart WeChat DevTools, import only the generated mp-weixin directory, compile, and verify the live result. Use for any uni-app / mini-program project.
---

# Rebuild Mini Program

Perform the complete rebuild and WeChat DevTools refresh flow. Do not treat hot reload, an old build directory, or a successful shell command alone as acceptance.

## Locate the build

Do not assume a fixed command or path. Detect them from the project:

1. Find the directory holding the mini-program's `package.json` (often `apps/miniprogram` in a monorepo, or the repo root).
2. From its `package.json` `scripts`, find the WeChat build script — usually `build:mp-weixin`. If absent, look for any script matching `build:mp*` and confirm with the user before running.
3. Run the build from that directory. The package manager is whatever the project uses (`pnpm` / `npm`); match the lockfile.
4. Record the generated mp-weixin artifact path — normally `<package-dir>/dist/build/mp-weixin` for uni-app. Verify it contains `app.json` and `project.config.json` before opening DevTools.

## Prepare WeChat DevTools

Use Computer Use for WeChat DevTools. Target controls by app, window, role, and visible label instead of screen coordinates.

1. In the app named `微信开发者工具`, clear the current project's compile/cache state with the visible cache controls when a project is open. Do not erase user data or repository files.
2. Return to the launcher and open `管理`. If an old `mp-weixin` recent-project entry exists, remove only that launcher entry. Never delete the repository or generated files from disk.
3. Fully quit WeChat DevTools with Command-Q or its application menu and confirm the app has exited. Closing only the project window is insufficient.

## Build

1. Do not read or print `.env` contents.
2. Run the build command recorded above. Require exit code 0 and a successful build message.
3. Confirm the generated `app.json` and `project.config.json` exist. If the build fails, stop and report the error; never open stale output as if it were current.

## Relaunch and import

1. Relaunch WeChat DevTools from the Dock or Applications and choose `导入`.
2. In the file chooser, use Go to Folder when available and select exactly the generated `mp-weixin` directory recorded above. Never import the repo root, `apps`, `dist`, or `build` — only the `mp-weixin` folder.
3. Before creating the project, verify that the chosen folder or proposed project name ends in `mp-weixin`. Select the test Mini Program identity already configured in WeChat DevTools without copying, logging, or hard-coding its identifier.
4. Activate `创建`. If prompted, activate `信任并运行`.

## Compile and verify

1. Verify the opened window title begins with `mp-weixin` rather than a parent directory name.
2. Select the `普通编译` mode and activate the `编译` control if compilation does not start automatically.
3. Wait for compilation to finish and then re-read the current UI state.
4. Require all of the following before reporting success:

   - no visible compile error or failure state;
   - the simulator renders the expected current Mini Program page;
   - the project remains identified as `mp-weixin`;
   - the verified WeChat DevTools page remains open for the user.

If the wrong directory was imported, close the incorrect project, remove only its launcher entry, and retry with the exact generated directory. If compilation fails, preserve the error evidence and stop without claiming success.
