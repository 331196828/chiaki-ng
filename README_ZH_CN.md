# chiaki-ng 简体中文本地化版

本分支以 [streetpea/chiaki-ng](https://github.com/streetpea/chiaki-ng) 的
`v1.10.0` 为基线（上游提交 `0c4a45d`），仅为桌面 Qt/QML 界面增加简体中文支持。
Remote Play、PSN、注册协议、RUDP、网络、音视频解码、Vulkan/OpenGL、FFmpeg 和控制器
输入逻辑均保持官方实现不变。

## 本地化方式

- 保持英文 `qsTr()` / C++ `tr()` 源字符串，英文为缺失条目的回退语言。
- 使用 Qt Linguist 的 `gui/i18n/chiaki_zh_CN.ts` 翻译目录；构建时生成并嵌入
  `chiaki_zh_CN.qm`。
- 设置的 **Config → Language** 可选择 `English` 或 `简体中文`。选择会保存在应用
  设置中，重启后生效。
- Windows 系统区域为 `zh_CN` 且用户尚未选择语言时，默认使用简体中文；其他系统默认英文。

Windows 使用 Qt 的系统字体回退，因此会优先使用系统内可用的中文字体。

## 构建 Windows x64

复用官方 MSYS2 工作流：`.github/workflows/build-msys2.yml`。本分支推送或以 `zh-cn-`
开头的 tag 会触发构建并上传 portable ZIP 与 Inno Setup installer。

本地 MSYS2 构建前需安装工作流列出的依赖（包含 `qt6-tools`，提供 `lupdate` 和
`lrelease`）：

```sh
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release -DCHIAKI_ENABLE_CLI=OFF
cmake --build build --target chiaki
```

更新翻译源字符串时，在构建目录运行：

```sh
cmake --build build --target chiaki_lupdate
```

然后用 Qt Linguist 编辑 `gui/i18n/chiaki_zh_CN.ts`，再重新构建以生成 `.qm`。

## 安装

从 GitHub Actions 下载 `chiaki-ng-win_x64-MSYS2-Release-installer.exe`，运行安装程序；
或下载 portable ZIP，解压后运行 `chiaki.exe`。

## 同步上游

将官方仓库设为 `upstream`，获取 tag/提交后在本分支上 rebase 或 merge。翻译只位于
`gui/i18n/`，其余改动限于 Qt GUI 初始化、GUI CMake 和语言设置，便于处理上游冲突：

```sh
git remote add upstream https://github.com/streetpea/chiaki-ng.git
git fetch upstream --tags
git rebase upstream/main
```
