# BlenderMCP - Blender Model Context Protocol Integration

[中文](README.md) | English

BlenderMCP connects Blender to Claude AI through the Model Context Protocol (MCP), allowing Claude to directly interact with and control Blender. This integration enables prompt assisted 3D modeling, scene creation, and manipulation.

**We have no official website. Any website you see online is unofficial and has no affiliation with this project. Use them at your own risk.**

[Full tutorial](https://www.youtube.com/watch?v=lCyQ717DuzQ)

### Join the Community

Give feedback, get inspired, and build on top of the MCP: [Discord](https://discord.gg/z5apgR8TFU)

### Supporters

[CodeRabbit](https://www.coderabbit.ai/)

**All supporters:**

[Support this project](https://github.com/sponsors/ahujasid)

## Current Version (1.5.5)

- Added Hunyuan3D support
- View screenshots for Blender viewport to better understand the scene
- Search and download Sketchfab models
- Support for Poly Haven assets through their API
- Support to generate 3D models using Hyper3D Rodin
- Run Blender MCP on a remote host
- Telemetry for tools executed (completely anonymous)

### Installing a New Version (Existing Users)

- If you are new here, go straight to Installation. Existing users can follow the notes below.
- Download the latest `addon.py` file and replace the older one, then add it to Blender.
- Delete the MCP server from Claude and add it back again.

## Features

- **Two-way communication**: Connect Claude AI to Blender through a socket-based server
- **Object manipulation**: Create, modify, and delete 3D objects in Blender
- **Material control**: Apply and modify materials and colors
- **Scene inspection**: Get detailed information about the current Blender scene
- **Code execution**: Run arbitrary Python code in Blender from Claude

## Components

The system consists of two main components:

1. **Blender Addon (`addon.py`)**: A Blender addon that creates a socket server within Blender to receive and execute commands
2. **MCP Server (`src/blender_mcp/server.py`)**: A Python server that implements the Model Context Protocol and connects to the Blender addon

## Installation

### Prerequisites

- Blender 3.0 or newer
- Python 3.10 or newer
- `uv` package manager

**If you're on Mac, install `uv` with**

```bash
brew install uv
```

**On Windows**

```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Then add `uv` to the user path in Windows (you may need to restart Claude Desktop afterwards):

```powershell
$localBin = "$env:USERPROFILE\.local\bin"
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$userPath;$localBin", "User")
```

Otherwise, check the official instructions here: [Install uv](https://docs.astral.sh/uv/getting-started/installation/)

**Do not proceed before installing `uv`.**

### Environment Variables

The following environment variables can be used to configure the Blender connection:

- `BLENDER_HOST`: Host address for the Blender socket server (default: `localhost`)
- `BLENDER_PORT`: Port number for the Blender socket server (default: `9876`)

Example:

```bash
export BLENDER_HOST='host.docker.internal'
export BLENDER_PORT=9876
```

### Claude for Desktop Integration

[Watch the setup instruction video](https://www.youtube.com/watch?v=neoK_WMq92g) (assuming you have already installed `uv`)

Go to `Claude > Settings > Developer > Edit Config > claude_desktop_config.json` and include the following:

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

Use the Claude Code CLI to add the Blender MCP server:

```bash
claude mcp add blender uvx blender-mcp
```

</details>

### Cursor Integration

[![Install MCP Server](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/link/mcp%2Finstall?name=blender&config=eyJjb21tYW5kIjoidXZ4IGJsZW5kZXItbWNwIn0%3D)

For Mac users, go to `Settings > MCP` and paste the following:

- To use it as a global server, click `add new global MCP server` and paste it there.
- To use it as a project-specific server, create `.cursor/mcp.json` in the project root and paste it there.

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

For Windows users, go to `Settings > MCP > Add Server`, then add a new server with the following settings:

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

[Cursor setup video](https://www.youtube.com/watch?v=wgWsJshecac)

**Only run one instance of the MCP server, either in Cursor or in Claude Desktop, not both.**

### Visual Studio Code Integration

_Prerequisites_: Make sure [Visual Studio Code](https://code.visualstudio.com/) is installed before proceeding.

[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_blender--mcp_server-0098FF?style=flat-square&logo=visualstudiocode&logoColor=ffffff)](vscode:mcp/install?%7B%22name%22%3A%22blender-mcp%22%2C%22type%22%3A%22stdio%22%2C%22command%22%3A%22uvx%22%2C%22args%22%3A%5B%22blender-mcp%22%5D%7D)

### Installing the Blender Addon

1. Download the `addon.py` file from this repository.
2. Open Blender.
3. Go to `Edit > Preferences > Add-ons`.
4. Click `Install...` and select the `addon.py` file.
5. Enable the addon by checking the box next to `Interface: Blender MCP`.

## Usage

### Starting the Connection

![BlenderMCP in the sidebar](assets/addon-instructions.png)

1. In Blender, go to the 3D View sidebar (`N` if it is not visible).
2. Find the `BlenderMCP` tab.
3. Turn on the Poly Haven checkbox if you want assets from their API (optional).
4. Click `Connect to Claude`.
5. Make sure the MCP server is running in your terminal.

### Using with Claude

Once the config file has been set in Claude, and the addon is running in Blender, you will see a hammer icon with tools for Blender MCP.

![BlenderMCP in the sidebar](assets/hammer-icon.png)

#### Capabilities

- Get scene and object information
- Create, delete, and modify shapes
- Apply or create materials for objects
- Execute arbitrary Python code in Blender
- Download matching models, assets, and HDRIs through [Poly Haven](https://polyhaven.com/)
- Generate 3D models through [Hyper3D Rodin](https://hyper3d.ai/)

### Example Commands

Here are some examples of what you can ask Claude to do:

- "Create a low poly scene in a dungeon, with a dragon guarding a pot of gold" [Demo](https://www.youtube.com/watch?v=DqgKuLYUv00)
- "Create a beach vibe using HDRIs, textures, and models like rocks and vegetation from Poly Haven" [Demo](https://www.youtube.com/watch?v=I29rn92gkC4)
- Give a reference image, and create a Blender scene out of it [Demo](https://www.youtube.com/watch?v=FDRb03XPiRo)
- "Generate a 3D model of a garden gnome through Hyper3D"
- "Get information about the current scene, and make a threejs sketch from it" [Demo](https://www.youtube.com/watch?v=jxbNI5L7AH8)
- "Make this car red and metallic"
- "Create a sphere and place it above the cube"
- "Make the lighting like a studio"
- "Point the camera at the scene, and make it isometric"

## Hyper3D Integration

Hyper3D's free trial key allows you to generate a limited number of models per day. If the daily limit is reached, wait for the reset on the next day or obtain your own key from [hyper3d.ai](https://hyper3d.ai/) and [fal.ai](https://fal.ai/).

## Troubleshooting

- **Connection issues**: Make sure the Blender addon server is running and the MCP server is configured in Claude. Do not run the `uvx` command manually in the terminal. Sometimes the first command does not go through, but subsequent ones work.
- **Timeout errors**: Try simplifying your requests or breaking them into smaller steps.
- **Poly Haven integration**: Claude can behave inconsistently.
- **Have you tried turning it off and on again?**: If you still have connection errors, restart both Claude and the Blender server.

## Technical Details

### Communication Protocol

The system uses a simple JSON-based protocol over TCP sockets:

- **Commands** are sent as JSON objects with a `type` and optional `params`
- **Responses** are JSON objects with a `status` and `result` or `message`

## Limitations & Security Considerations

- The `execute_blender_code` tool allows running arbitrary Python code in Blender. It is powerful, but potentially dangerous. Use it with caution in production environments, and always save your work first.
- Poly Haven requires downloading models, textures, and HDRI images. If you do not want to use it, turn it off using the checkbox in Blender.
- Complex operations may need to be broken down into smaller steps.

#### Telemetry Control

BlenderMCP collects anonymous usage data to help improve the tool. You can control telemetry in two ways:

1. **In Blender**: Go to `Edit > Preferences > Add-ons > Blender MCP` and uncheck the telemetry consent checkbox.
   - With consent enabled: collects anonymized prompts, code snippets, and screenshots
   - With consent disabled: only collects minimal anonymous usage data (tool names, success/failure, duration)
2. **Environment variable**: Completely disable all telemetry by running:

```bash
DISABLE_TELEMETRY=true uvx blender-mcp
```

Or add it to your MCP config:

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

All telemetry data is fully anonymized and used solely to improve BlenderMCP.

## Contributing

Contributions are welcome. Feel free to submit a Pull Request.

## Disclaimer

This is a third-party integration and is not made by Blender. Created by [Siddharth](https://x.com/sidahuj).
