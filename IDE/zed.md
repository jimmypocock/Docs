# Zed

## Description
Zed is a high-performance, multiplayer code editor written from scratch in Rust by the creators of Atom and Tree-sitter. It leverages multiple CPU cores and GPU acceleration for blazing-fast performance while featuring real-time collaboration, native AI integration, and a clean, minimalist interface.

## URL/Installation
- Website: https://zed.dev/
- GitHub: https://github.com/zed-industries/zed
- Installation:
  - **macOS**: Download from website or `brew install zed`
  - **Linux**: `curl -f https://zed.dev/install.sh | sh`
  - **Windows**: Build from source (official release coming soon)

## Tags
#tool #editor #beginner #intermediate #open-source #free #cross-platform #performance #collaboration #ai #rust #gpu-accelerated #multiplayer #real-time

## Use Cases
- High-performance code editing and development
- Real-time collaborative coding sessions
- AI-assisted programming with multiple LLM integrations
- Large project management with fast file operations
- Multi-language development with LSP support
- Remote development and pair programming
- Git workflow management with native integration

## Key Features
- **Blazing Fast Performance**: Written in Rust with GPU acceleration for instant startup and smooth editing
- **Real-time Collaboration**: Built-in multiplayer editing with voice chat and screen sharing
- **Native AI Integration**: Agent Panel with support for multiple LLMs (Claude, GPT, Ollama, etc.)
- **Multi-language Support**: Tree-sitter, WebAssembly, and LSP support for syntax highlighting and intelligent features
- **Multibuffers**: Compose excerpts from across the codebase in one editable surface
- **Native Git Integration**: First-class support for staging, committing, pulling, pushing, and viewing diffs
- **Vim Mode**: Full modal editing support with Vim bindings
- **Remote Development**: Run UI locally while keeping codebase on remote server
- **Language Server Integration**: Automatic installation and configuration of language servers

## Installation/Setup

### macOS
```bash
# Download from website or use Homebrew
brew install zed

# Or download .dmg from https://zed.dev/download
```

### Linux
```bash
# Using install script
curl -f https://zed.dev/install.sh | sh

# Or using package managers (varies by distribution)
# Check https://zed.dev/docs/linux for specific instructions
```

### Windows
```bash
# Build from source (official release pending)
git clone https://github.com/zed-industries/zed.git
cd zed
cargo build --release
```

### CLI Setup
```bash
# Install CLI tool (from within Zed app)
# Go to Zed > Install CLI in application menu

# Open directory/file from terminal
zed my-project/
zed file.txt

# Use as $EDITOR for Git commits
export EDITOR="zed --wait"
```

## Basic Usage

### Command Palette
```
cmd-shift-p / ctrl-shift-p  # Open command palette (most important shortcut)
```

### File Operations
```
cmd-o / ctrl-o              # Open file
cmd-n / ctrl-n              # New file
cmd-s / ctrl-s              # Save file
cmd-shift-n / ctrl-shift-n  # New window
```

### Navigation
```
cmd-p / ctrl-p              # Go to file
cmd-shift-f / ctrl-shift-f  # Search in project
cmd-g / ctrl-g              # Go to line
```

### AI Features
```
cmd-enter / ctrl-enter      # Open AI assistant
cmd-shift-c / ctrl-shift-c  # Collaboration panel
```

## Configuration

### Settings (settings.json)
```json
{
  "theme": "Andromeda",
  "buffer_font_family": "Fira Code",
  "buffer_font_size": 16,
  "vim_mode": true,
  "relative_line_numbers": true,
  "tab_size": 2,
  "format_on_save": "on",
  "autosave": "on_focus_change",
  "soft_wrap": "preferred_line_length",
  "preferred_line_length": 80,
  "git_panel": {
    "dock": "right"
  },
  "assistant": {
    "default_model": {
      "provider": "anthropic",
      "model": "claude-3-5-sonnet-20241022"
    }
  },
  "languages": {
    "JavaScript": {
      "tab_size": 2,
      "format_on_save": "on"
    },
    "Python": {
      "format_on_save": "language_server"
    }
  }
}
```

### Keybindings (keymap.json)
```json
[
  {
    "context": "Workspace",
    "bindings": {
      "cmd-t": "file_finder::Toggle",
      "cmd-shift-t": "task::Spawn"
    }
  },
  {
    "context": "Editor",
    "bindings": {
      "ctrl-d": "editor::DuplicateLine",
      "alt-up": "editor::MoveLineUp"
    }
  }
]
```

## Alternatives
- **VS Code**: Most popular, extensive extension ecosystem, but slower performance
- **Sublime Text**: Fast and stable, but proprietary and limited collaboration features
- **Vim/Neovim**: Highly customizable and lightweight, but steeper learning curve
- **Atom**: Predecessor by same team, now discontinued
- **Cursor**: AI-focused VS Code fork with similar features to Zed's AI capabilities
- **Fleet**: JetBrains' next-gen editor with collaboration features

## Pros/Cons
**Pros:**
- Exceptional performance and speed (fastest editor startup times)
- Built-in real-time collaboration without plugins
- Native AI integration with multiple providers
- Clean, distraction-free interface
- Excellent Git integration
- Growing rapidly with active development
- Free and open-source
- Cross-platform (macOS, Linux, Windows support coming)
- Memory efficient compared to Electron-based editors

**Cons:**
- Limited extension ecosystem compared to VS Code
- Windows support still in development
- Smaller community and fewer third-party resources
- Some advanced IDE features not yet available
- Still in active development (beta phase)
- Limited themes compared to mature editors
- No stable Windows release yet

## Date Added
2025-05-14

## Last Updated
2025-05-14

## Notes
- Zed excels at speed and collaboration but may not be suitable for users heavily dependent on specific VS Code extensions
- The editor is particularly strong for JavaScript/TypeScript, Rust, and Python development
- AI features require API keys for external providers or use of Zed Pro subscription
- Vim mode implementation is comprehensive and includes advanced features like text objects
- Real-time collaboration includes voice chat and screen sharing capabilities
- GPU acceleration requires Vulkan support on Linux
- Extension system is growing but still limited compared to mature editors
- Best suited for developers who value performance and collaboration over extensive customization