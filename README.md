# rebuild-miniprogram

源码变更后重新编译微信小程序，并在微信开发者工具里验证真实运行结果——不是只跑一下 build 命令就当成成功了。

## 为什么需要

开发者工具会持续加载一个看起来是最新的旧编译产物。热重载可能静默失败，build 命令退出码为 0 也不代表模拟器已经加载了新代码。对着一个过时的产物调试，白白浪费好几个小时。

这个 skill 跑真正的构建，完全重启开发者工具，只导入新生成的 `mp-weixin` 目录，编译，然后验证结果确实是实时的。

## 安装

### 方式 A — 交给 agent 安装

把仓库地址给你的 agent，让它安装：

```
https://github.com/sabrina-fan/rebuild-miniprogram
```

### 方式 B — 手动安装

把 `rebuild-miniprogram/` 目录复制到你的 agent skill 目录下（如 `~/.zcode/skills/`）。

## 依赖

- 已安装微信开发者工具并可访问。
- 一个能产出 `mp-weixin` 构建产物的项目（uni-app 或任何兼容框架）。
- Computer Use 能力，用于驱动开发者工具 GUI（skill 按 app/window/label 定位控件，不用屏幕坐标）。

## 使用方法

自然语言触发即可——"刷新一下小程序"、"看下最新效果"、"rebuild the mini program"，或任何开发者工具可能显示旧产物的情况。

skill 会依次执行：

1. 从 `package.json` 检测构建命令和产物路径。
2. 跑构建，确认产物包含 `app.json` 和 `project.config.json`。
3. 完全退出开发者工具，清理旧缓存，重新导入新生成的 `mp-weixin` 目录。
4. 编译并验证四个成功条件：无编译错误、模拟器渲染预期页面、项目标识为 `mp-weixin`、窗口保持打开。

## 兼容性

- **macOS** — 主要平台（用 Computer Use 驱动开发者工具 GUI）。
- **Windows / Linux** — 如果你的 agent 有等效的 GUI 自动化能力，应该也能用，但未正式测试。

## 安全与边界

这个 skill 只负责**重新构建 + 验证**。不写或改源码、不调试业务逻辑、不跑单元测试。
