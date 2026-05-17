# BlenderMCP - Blender 模型上下文协议集成

中文 | [English](README_EN.md)

BlenderMCP 通过模型上下文协议（MCP）将 Blender 与 Claude AI 连接起来，使 Claude 可以直接与 Blender 交互并进行控制。该集成支持通过自然语言提示来辅助 3D 建模、场景创建与场景编辑。

**我们没有官方网站。你在网上看到的任何网站都不是官方站点，也与本项目无关，使用风险需自行承担。**

[完整教程](https://www.youtube.com/watch?v=lCyQ717DuzQ)

### 加入社区

欢迎通过 [Discord](https://discord.gg/z5apgR8TFU) 提供反馈、获取灵感，并基于这个 MCP 继续扩展。

### 支持者

[CodeRabbit](https://www.coderabbit.ai/)

**全部支持者：**

[赞助此项目](https://github.com/sponsors/ahujasid)

## 当前版本（1.5.5）

- 新增 Hunyuan3D 支持
- 支持查看 Blender 视口截图，更好地理解当前场景
- 支持搜索并下载 Sketchfab 模型
- 支持通过 Poly Haven API 获取资源
- 支持通过 Hyper3D Rodin 生成 3D 模型
- 支持在远程主机上运行 Blender MCP
- 支持工具执行遥测（完全匿名）

### 安装新版本（已有用户）

- 新用户可直接查看下方“安装”章节，已有用户请按照以下步骤更新。
- 下载最新的 `addon.py` 文件，替换旧版本后重新添加到 Blender。
- 在 Claude 中删除原有 MCP 服务器配置，再重新添加一次即可。

## 功能特性

- **双向通信**：通过基于 Socket 的服务把 Claude AI 连接到 Blender
- **对象操作**：在 Blender 中创建、修改和删除 3D 对象
- **材质控制**：应用和修改材质及颜色
- **场景检查**：获取当前 Blender 场景的详细信息
- **代码执行**：让 Claude 在 Blender 中执行任意 Python 代码

## 组件说明

整个系统由两个主要部分组成：

1. **Blender 插件（`addon.py`）**：在 Blender 内启动一个 Socket 服务器，用于接收并执行命令
2. **MCP 服务器（`src/blender_mcp/server.py`）**：实现 Model Context Protocol 的 Python 服务，并与 Blender 插件建立连接

## 安装

### 前置要求

- Blender 3.0 或更高版本
- Python 3.10 或更高版本
- `uv` 包管理器

**如果你使用 Mac，请通过以下命令安装 `uv`：**

```bash
brew install uv
```

**Windows 用户：**

```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

然后将 `uv` 添加到 Windows 用户路径中（你可能需要重启 Claude Desktop）：

```powershell
$localBin = "$env:USERPROFILE\.local\bin"
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$userPath;$localBin", "User")
```

其他安装方式请参考官方文档：[安装 uv](https://docs.astral.sh/uv/getting-started/installation/)

**在安装好 `uv` 之前，请不要继续后续步骤。**

### 环境变量

你可以通过以下环境变量来配置 Blender 连接：

- `BLENDER_HOST`：Blender Socket 服务器地址，默认值为 `localhost`
- `BLENDER_PORT`：Blender Socket 服务器端口，默认值为 `9876`

示例：

```bash
export BLENDER_HOST='host.docker.internal'
export BLENDER_PORT=9876
```

### Claude Desktop 集成

[观看配置教学视频](https://www.youtube.com/watch?v=neoK_WMq92g)（默认你已经安装好了 `uv`）

打开 `Claude > Settings > Developer > Edit Config > claude_desktop_config.json`，加入以下内容：

```json
{
    "mcpServers": {
        "blender": {
            "command": "uvx",
            "args": [
                "blender-mcp"
            ]
        }
    }
}
```

<details>
<summary>Claude Code</summary>

也可以通过 Claude Code CLI 添加 Blender MCP 服务器：

```bash
claude mcp add blender uvx blender-mcp
```

</details>

### Codex CLI / IDE 扩展集成

根据 OpenAI 官方文档，Codex CLI 与 IDE 扩展共享同一份 MCP 配置，因此只需要配置一次。

1. 确保 `codex` 与 `uvx` 都已经安装，并且可以在终端中直接运行。
2. 运行以下命令，把 Blender MCP 添加到 Codex：

```bash
codex mcp add blender_mcp -- uvx blender-mcp
```

3. 用以下命令确认配置已经生效：

```bash
codex mcp list
```

4. 如果 Codex CLI、IDE 扩展或 Codex App 已经打开，请重启它们以重新加载 MCP 配置。

你也可以手动编辑 `~/.codex/config.toml`：

```toml
[mcp_servers.blender_mcp]
command = "uvx"
args = ["blender-mcp"]
```

如果你刚在 Windows 上把 `uv` 加入 PATH，记得先重启终端，必要时再重启 Codex。

### Cursor 集成

[![Install MCP Server](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/link/mcp%2Finstall?name=blender&config=eyJjb21tYW5kIjoidXZ4IGJsZW5kZXItbWNwIn0%3D)

Mac 用户请进入 `Settings > MCP`，然后粘贴以下配置：

- 如果要作为全局服务器使用，点击 `add new global MCP server` 后粘贴。
- 如果要作为项目级服务器使用，请在项目根目录创建 `.cursor/mcp.json` 并粘贴。

```json
{
    "mcpServers": {
        "blender": {
            "command": "uvx",
            "args": [
                "blender-mcp"
            ]
        }
    }
}
```

Windows 用户请进入 `Settings > MCP > Add Server`，并使用以下配置新增服务器：

```json
{
    "mcpServers": {
        "blender": {
            "command": "cmd",
            "args": [
                "/c",
                "uvx",
                "blender-mcp"
            ]
        }
    }
}
```

[Cursor 配置视频](https://www.youtube.com/watch?v=wgWsJshecac)

**MCP 服务器只能运行一个实例，要么在 Cursor 中运行，要么在 Claude Desktop 中运行，不要同时运行。**

### Visual Studio Code 集成

_前置条件_：继续之前，请先确保你已经安装 [Visual Studio Code](https://code.visualstudio.com/)。

[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_blender--mcp_server-0098FF?style=flat-square&logo=visualstudiocode&logoColor=ffffff)](vscode:mcp/install?%7B%22name%22%3A%22blender-mcp%22%2C%22type%22%3A%22stdio%22%2C%22command%22%3A%22uvx%22%2C%22args%22%3A%5B%22blender-mcp%22%5D%7D)

### 安装 Blender 插件

1. 从本仓库下载 `addon.py`
2. 打开 Blender
3. 进入 `Edit > Preferences > Add-ons`
4. 点击 `Install...`，选择 `addon.py`
5. 勾选 `Interface: Blender MCP` 以启用插件

## 使用方法

### 启动连接

![BlenderMCP in the sidebar](assets/addon-instructions.png)

1. 在 Blender 中打开 3D 视图侧边栏（如果没显示，按 `N`）
2. 找到 `BlenderMCP` 标签页
3. 如果你希望从 Poly Haven API 获取资源，勾选对应选项（可选）
4. 点击 `Connect to Claude`
5. 确保 MCP 服务器已经在终端中运行

### 与 Claude 配合使用

当 Claude 的配置文件设置完成、Blender 插件也已运行后，你会看到一个带有 Blender MCP 工具的锤子图标。

![BlenderMCP in the sidebar](assets/hammer-icon.png)

#### 能力范围

- 获取场景和对象信息
- 创建、删除和修改几何体
- 为对象创建或应用材质
- 在 Blender 中执行任意 Python 代码
- 通过 [Poly Haven](https://polyhaven.com/) 下载合适的模型、素材和 HDRI
- 通过 [Hyper3D Rodin](https://hyper3d.ai/) 生成 AI 3D 模型

### 与 Codex 配合使用

1. 先按上文步骤把 `blender_mcp` 添加到 Codex。
2. 按下文步骤安装并启用 Blender 插件 `addon.py`。
3. 在 Blender 侧边栏点击 `Connect to Claude`。按钮名称虽然写的是 Claude，但它启动的是 Blender 端的 Socket 服务，Codex 同样使用这个连接。
4. 在 Codex CLI、IDE 扩展或 Codex App 中打开你的项目，然后直接让 Codex 调用 Blender MCP 工具。
5. 建议同一时间只使用一个 Blender MCP 客户端，避免和 Claude、Cursor 等同时连接。

你可以直接给 Codex 这样的指令：

- “创建一个低多边形城堡庭院场景”
- “把当前选中的物体改成磨砂金属材质”
- “下载一个适合沙漠场景的 HDRI 并应用到当前场景”
- “查看当前场景并把主相机调整成等轴测视角”

### 示例指令

以下是一些你可以让 Claude 执行的示例：

- “创建一个低多边形地牢场景，让一条龙守着一锅金币” [Demo](https://www.youtube.com/watch?v=DqgKuLYUv00)
- “使用来自 Poly Haven 的 HDRI、纹理以及岩石和植被等模型，营造海滩氛围” [Demo](https://www.youtube.com/watch?v=I29rn92gkC4)
- 提供一张参考图，让它据此生成一个 Blender 场景 [Demo](https://www.youtube.com/watch?v=FDRb03XPiRo)
- “通过 Hyper3D 生成一个花园地精 3D 模型”
- “获取当前场景信息，并基于它制作一个 threejs 草图” [Demo](https://www.youtube.com/watch?v=jxbNI5L7AH8)
- “把这辆车改成红色金属漆”
- “创建一个球体并放到立方体上方”
- “把灯光调整成摄影棚风格”
- “让相机对准场景，并改成等轴测视角”

## Hyper3D 集成

Hyper3D 的免费试用密钥每天只能生成有限数量的模型。如果当天额度已用完，可以等到第二天重置，或者自行从 [hyper3d.ai](https://hyper3d.ai/) 和 [fal.ai](https://fal.ai/) 获取密钥。

## 故障排查

- **连接问题**：确认 Blender 插件服务器已启动，并且 Claude 中已经正确配置 MCP 服务器。不要手动在终端运行 `uvx` 命令。有时第一条命令可能不会成功，之后通常会恢复正常。
- **超时错误**：尝试简化请求，或者将任务拆成更小的步骤。
- **Poly Haven 集成**：Claude 在这部分的行为有时会不太稳定。
- **重启大法**：如果仍然有连接错误，请同时重启 Claude 和 Blender 服务。

## 技术细节

### 通信协议

系统使用基于 TCP Socket 的简单 JSON 协议：

- **命令**：以 JSON 对象发送，包含 `type` 和可选的 `params`
- **响应**：以 JSON 对象返回，包含 `status`，以及 `result` 或 `message`

## 限制与安全注意事项

- `execute_blender_code` 工具允许在 Blender 中执行任意 Python 代码。能力很强，但也存在风险。请在生产环境中谨慎使用，并且务必提前保存工作内容。
- Poly Haven 需要下载模型、纹理和 HDRI 图片。如果你不想使用它，请在 Blender 中取消勾选对应选项。
- 复杂操作可能需要拆分为多个更小的步骤执行。

#### 遥测控制

BlenderMCP 会收集匿名使用数据，以帮助改进工具。你可以通过以下两种方式控制遥测：

1. **在 Blender 内关闭**：进入 `Edit > Preferences > Add-ons > Blender MCP`，取消勾选遥测同意选项
   - 勾选同意时：收集匿名化的提示词、代码片段和截图
   - 未勾选同意时：只收集最小化匿名使用数据（工具名、成功或失败、耗时）
2. **使用环境变量**：通过以下方式彻底禁用所有遥测

```bash
DISABLE_TELEMETRY=true uvx blender-mcp
```

或者把它加入 MCP 配置：

```json
{
    "mcpServers": {
        "blender": {
            "command": "uvx",
            "args": ["blender-mcp"],
            "env": {
                "DISABLE_TELEMETRY": "true"
            }
        }
    }
}
```

所有遥测数据都会进行完全匿名化处理，且仅用于改进 BlenderMCP。

## 贡献

欢迎贡献代码，直接提交 Pull Request 即可。

## 免责声明

这是一个第三方集成项目，并非 Blender 官方产品。作者是 [Siddharth](https://x.com/sidahuj)。
