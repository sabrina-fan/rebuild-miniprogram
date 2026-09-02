# rebuild-miniprogram

改完微信小程序代码后，走完整的"重建 + 刷新微信开发者工具"流程的 AI agent skill。不接受热重载、旧产物或命令成功就算通过。

自动探测项目的构建命令和 mp-weixin 产物路径（uni-app 一般在 `dist/build/mp-weixin`），校验 `app.json`/`project.config.json` 存在才往下走；清缓存只清 file/compile（不碰 storage/auth/all）；Cmd-Q 彻底退出开发者工具；只导入生成的 mp-weixin 目录；编译后验证窗口标题、无报错、模拟器渲染、项目名四要素，失败保留证据不假装成功。

适用于任何 uni-app / 微信小程序项目。

## 安装

把 `SKILL.md` 复制到你的 agent skill 目录下即可。

## License

MIT
