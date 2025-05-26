# asdf

## Description

asdf is a CLI tool that manages multiple runtime versions for various programming languages and tools with a single interface, allowing developers to easily switch between different versions per project.

## URL/Installation

- Website: <https://asdf-vm.com/>
- GitHub: <https://github.com/asdf-vm/asdf>
- Installation: `git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.0`

## Tags

# tool #devops #cli #version-manager #intermediate #cross-platform #open-source #free

## Use Cases

- Managing multiple versions of programming languages (Node.js, Python, Ruby, etc.) on a single system
- Ensuring consistent development environments across teams
- Setting up project-specific runtime versions using .tool-versions configuration files
- Simplifying workflow when switching between projects with different version requirements
- Supporting development across multiple technologies with a unified interface

## Key Features

- Single CLI tool to manage multiple language runtimes
- Plugin system supporting 100+ languages and tools
- Project-specific runtime versions using .tool-versions configuration
- Global and local version configuration
- Automatic switching between versions when changing directories
- Shell completion for bash, zsh, and fish
- Integration with existing language version files (.node-version, .ruby-version, etc.)
- Extensible plugin architecture

## Installation/Setup

### macOS

```bash
# Install with Homebrew
brew install asdf

# Add to shell (bash)
echo -e "\n. $(brew --prefix asdf)/libexec/asdf.sh" >> ${BASH_PROFILE:-~/.bash_profile}

# Add to shell (zsh)
echo -e "\n. $(brew --prefix asdf)/libexec/asdf.sh" >> ${ZDOTDIR:-~/.zshrc}
```

### Linux

```bash
# Install dependencies (Ubuntu/Debian)
apt install curl git

# Clone repository
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.0

# Add to shell (bash)
echo -e "\n. $HOME/.asdf/asdf.sh" >> ~/.bashrc

# Add to shell (zsh)
echo -e "\n. $HOME/.asdf/asdf.sh" >> ~/.zshrc
```

### Windows (via Git Bash)

```bash
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.0
echo -e "\n. $HOME/.asdf/asdf.sh" >> ~/.bashrc
```

## Basic Usage

```bash
# Add a plugin for a language
asdf plugin add nodejs

# Install a specific version
asdf install nodejs 18.16.0

# Set a global version
asdf global nodejs 18.16.0

# Set a local (project) version
asdf local nodejs 16.20.0

# List installed versions
asdf list nodejs

# Check current version
asdf current nodejs

# Uninstall a version
asdf uninstall nodejs 14.21.3

# Update all plugins
asdf plugin update --all
```

## Configuration

Project-specific configuration in .tool-versions:

```
nodejs 18.16.0
python 3.10.0
ruby 3.2.2
```

Global configuration in ~/.tool-versions:

```
nodejs 18.16.0
python 3.11.0
ruby 3.2.2
golang 1.20.4
```

## Alternatives

- nvm: Node.js specific version manager, simpler but limited to Node.js only
- pyenv: Python-specific version manager with similar functionality but limited to Python
- rbenv: Ruby-specific version manager with good performance but single-language focus
- gvm: Go Version Manager for managing multiple Go installations
- sdkman: Version manager focused on JVM-based languages and tools
- mise: A newer alternative to asdf, claims to be faster with enhanced features

## Pros/Cons

**Pros:**

- Single tool for managing multiple language runtimes
- Consistent interface across different programming languages
- Project-specific version management through .tool-versions file
- Extensive plugin ecosystem (supports 100+ languages and tools)
- Active community and development
- Shell completion for faster command entry
- Works on macOS, Linux, and Windows (via Git Bash)
- Lightweight with minimal dependencies

**Cons:**

- Initial setup can be more complex than language-specific managers
- Requires installing plugins for each language individually
- Some plugins may lag behind the latest version releases
- Performance can be slower than some language-specific managers
- Windows support is somewhat limited (requires Git Bash)
- Learning curve for developers used to language-specific tools

## Date Added

2025-05-19

## Last Updated

2025-05-19

## Notes

asdf uses a plugin system for adding support for different programming languages and tools. The community actively maintains these plugins, but quality and update frequency can vary.

The .tool-versions file can be committed to version control to ensure all developers on a project use the same runtime versions.

For backward compatibility, asdf can read language-specific version files like .node-version or .ruby-version, making transition from other version managers easier.

When working with teams, the .tool-versions file provides a simple way to standardize development environments across all team members.
