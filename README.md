# 模组引擎 Mod Engine v2 

> [!IMPORTANT]
> Development on Mod Engine 2 is discontinued. Any future work will be done on [me3](https://github.com/garyttierney/me3).
> 
> **重要提示**
> Mod Engine 2 的开发已停止。未来的任何工作都将在 [me3](https://github.com/garyttierney/me3) 上进行。

Mod Engine 2 is a ground up rewrite of Mod Engine, a runtime code patching and injection library used for adding modding functionality to the Souls games by FROM Software.

模组引擎2 是对上个版本的全新重写，这是一个用于添加 FROM Software 游戏改装功能的运行时代码修补和注入库。

# 目录 Table of Contents
# 
1. [Get started 开始](#get-started-guide)
2. [Supported games 支持](#supported-games)
2. [Differences 异同](#what-are-the-differences)
3. [Features 特性](#features)

## 入门指南 Get started guide

1. Download the latest [release](https://github.com/Lioncky/ModEngine2/releases).
2. Create a folder in the mod folder, I recommend the name of the mod for clarity.
3. Unpack the mod into the just created folder.
4. Edit the `config_eldenring.toml` (or your game of choice) to include the folder you just created.

1. 下载最新的[发行版](https://github.com/Lioncky/ModEngine2/releases)。
2. 在 mod 文件夹中创建一个文件夹，建议使用 mod 的名称以便清晰。
3. 将 mod 解压到刚创建的文件夹中。
4. 编辑 `config_eldenring.toml`（或你选择的游戏配置文件），将你刚创建的文件夹包含进去。

```toml
mods = [
  { enabled = true, name = "BetterAshes", path = "mod\\ashes" },
  { enabled = true, name = "CleversMoveset", path = "mod\\moveset" },
  { enabled = false, name = "EnemyRandomizer", path = "mod\\randomizer" },
]
```

<details>
  <summary>Folder structure example 文件夹结构 (CLICK ME 点我!)</summary>

  ![folder exmaple image](docs/media/mod-engine-mod-folder-example.png)

</details>
## 支持的游戏 Supported games

See the list below for information on games that currently have support in Mod Engine 2.

请参阅下面的列表，了解 Mod Engine 2 当前支持的游戏。

- [x] Dark Souls 3 黑魂3
- [x] Elden Ring 艾尔登法环
- [ ] Dark Souls 2: SOTF 魂2
- [ ] Dark Souls Remastered 魂1
- [ ] Sekiro: Shadows Die Twice 只狼
- [ ] Bloodborne 血缘诅咒

## 新版异同点 What are the differences?

There are some big architecture differences between legacy Mod Engine and the new version, the bulk of which is in 1) the configuration format and 2) how we get ModEngine loaded into the game.
More details about these differences are listed below.

旧版 Mod Engine 和新版本之间在架构上有一些重大差异，主要体现在 1) 配置格式 和 2) 我们如何将 ModEngine 加载到游戏中。
下面列出了关于这些差异的更多细节。

### 新配置格式 New configuration format

The old `.ini` format is gone, replaced by a new structured configuration file based on [TOML](https://toml.io/en/).
This change is to suit the new extension model of Mod Engine by allowing plugins to specify their own configuration requirements.
旧的 `.ini` 格式已被基于 [TOML](https://toml.io/en/) 的新结构化配置文件取代。
此更改是为了适应 Mod Engine 的新扩展模型，允许插件指定自己的配置要求。

This is used by the mod loader plugin to specify lists of mods that should be loaded and whether they should be enabled or not.
It may be extended in future to support a "Mod Manifest", which would contain additional information like website, version, authors, etc.

mod 加载器插件使用它来指定应加载的 mod 列表以及是否启用它们。
未来可能会扩展以支持 "Mod Manifest"，其中将包含网站、版本、作者等附加信息。

### Sideloading as `dinput8.dll` is optional
### 作为 `dinput8.dll` 的旁加载是可选的 
This fork fix ER crash while sideloading.

这个fork修正了法环不能正常加载的问题

With the introduction of a launcher we no longer need to rely on games loading via `dinput8.dll` and we can instead launch the game pre-configured.
This frees up this module for anything else that relies on being sideloaded.

随着启动器的引入，我们不再需要依赖通过 `dinput8.dll` 加载游戏，而是可以预先配置启动游戏。
这使得该模块可以用于其他任何依赖旁加载的内容。

### 多模组支持 Support for loading multiple mods

Multiple game roots can be specified in configuration, allowing users to run multiple mods concurrently without replacing files in their mod folder manually.
Note, however, that this is still restricted to mods that don't replace conflicting files.

可以在配置中指定多个游戏根目录，允许用户同时运行多个 mod，而无需手动替换 mod 文件夹中的文件。
但是，请注意，这仍然仅限于不替换冲突文件的 mod。

### Focus on tooling for mod creators
### 专注于为 mod 创作者提供工具

The primary driver behind development of Mod Engine 2 is creating a tool that can be used to rapidly reverse the games that we are interested in.
To this end, we offer functionality to make the reversing process easier:

开发 Mod Engine 2 的主要动机是创建一个工具，可以用于快速逆向我们感兴趣的游戏。
为此，我们提供了一些功能，使逆向过程更容易：

- Integration with Optick Profiler
- Runtime scripting and live code patching
- Crash dumps for all users
- 与 Optick Profiler 集成
- 运行时脚本和实时代码修补
- 所有用户的崩溃转储

## 功能 Features
Mod Engine 2 covers all existing functionality from the original Mod Engine for Dark Souls but introduces some important new features that mod authors should be aware of.

模组引擎2 包含了初版为《黑暗之魂》提供的所有现有功能，但引入了一些 mod 作者应注意的重要新功能。

### Exe模组加载器 Mod Launcher

We have created a launcher application that is designed to boot games with your mods pre-installed without the user having to do any manual file copying on their side.
Historically, mods would live in the same location as the game folder and Mod Engine would attempt to redirect requests for game files to mod file paths within subdirectories of this game folder.
This deployment model was inflexible and required a lot of manual tweaking if a user wanted to have multiple mods installed, switching between them as the choose.

我们创建了一个启动器应用程序，旨在在用户无需手动复制文件的情况下启动预装了 mod 的游戏。
历史上，mod 会与游戏文件夹位于同一位置，Mod Engine 会尝试将游戏文件的请求重定向到此游戏文件夹的子目录中的 mod 文件路径。
这种部署模型不灵活，如果用户希望安装多个 mod 并在它们之间切换，则需要大量手动调整。

To solve this problem `modengine2_launcher` was created.
This simple command-line application has 3 main objectives.
为了解决这个问题，我们创建了 `modengine2_launcher`。
这个简单的命令行应用程序有三个主要目标。

1. Find where a user has installed the game 找到用户安装游戏的位置
2. Start the game with modengine2.dll already loaded 在已加载 modengine2.dll 的情况下启动游戏
3. Pass information to modengine2.dll about the configuration the user launched the game with 将用户启动游戏时的配置信息传递给 modengine2.dll

This allows us to keep mods, modengine, and the game itself completely detached.
As a result, running the game directly from Steam will always result in a vanilla instance being launched.

这使我们能够将 mod、modengine 和游戏本身完全分离。
因此，直接从 Steam 运行游戏将始终启动一个原始实例。

### 扩展/插件支持 Extension/plugin support

Mod Engine 2 introduces support for extensions and plugins which are designed to replace the old chainloading mechanism from legacy Mod Engine.
Extensions are able to use core functionality to register patches, code hooks, and interact with other extensions.
This will eventually be extended to support runtime Lua scripting in the future.
Mod Engine 2 引入了对扩展和插件的支持，这些扩展和插件旨在取代旧版 Mod Engine 的旧链加载机制。
扩展能够使用核心功能注册补丁、代码挂钩并与其他扩展交互。
这最终将扩展为支持运行时 Lua 脚本。

### 调试器支持 Debugger support

ScyllaHide is included with Mod Engine 2 distributions and allows debugging games via native debuggers without worrying about evading anti-debug techniques using programs like Cheat Engine.
Instead, you can use your favourite debugger (WinDbg, x64dbg, or even Cheat Engine without relying on VEH support).
模组引擎 2 发行版中包含 ScyllaHide，允许通过本机调试器调试游戏，而无需担心使用 Cheat Engine 等程序规避反调试技术。
相反，你可以使用你喜欢的调试器（WinDbg、x64dbg，无需不依赖 VEH 支持的 Cheat Engine）。

### 脚本 Lua scripting


### 编译 Build 
git clone https://github.com/Lioncky/ModEngine2.git --recurse-submodules
1. 下载并构建 Detours [vs2019 ToolCmd x86_64 nmake]
2. 安装vcpkg和依赖  [vcpkg install lua spdlog fmt crashpad sol2]
3. 指定cmakeToolChain [cmake -B build -DCMAKE_TOOLCHAIN_FILE=yourpath/vcpkg/scripts/buildsystems/vcpkg.cmake]
4. 在vs2019或vs2022中进行构建 Release版本需要删除debuglib的路径 这里有错误
5. 重定向目录到输出

**正在开发中的内容 WIP**
