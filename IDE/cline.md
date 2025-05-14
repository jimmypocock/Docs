# Cline

## Description
Cline is an open-source AI autonomous coding agent for VS Code that can create/edit files, execute terminal commands, use the browser, and integrate with external tools through the Model Context Protocol (MCP), all with human-in-the-loop approval.

## URL/Installation
- Website: https://cline.bot/
- GitHub: https://github.com/cline/cline
- Documentation: https://docs.cline.bot/
- Installation: Install from VS Code Marketplace by searching "Cline"

## Tags
#tool #ai #ide #extension #vscode #coding-assistant #autonomous #cli #open-source #free #intermediate #advanced

## Use Cases
- Autonomous code generation and refactoring across multiple files
- Terminal command execution with error handling and debugging
- Browser automation for testing and visual debugging
- Converting mockups/screenshots into functional applications
- Full-stack application development from scratch
- Integration with external services via Model Context Protocol (MCP)
- Code analysis and bug fixing with context awareness
- Real-time collaborative coding with AI assistance

## Key Features
- **Dual Plan/Act Modes**: Switch between planning and execution modes for different task complexities
- **Browser Integration**: Computer Use capability with Anthropic Claude for visual debugging and testing
- **Terminal Execution**: Direct command execution with shell integration and error handling
- **Model Context Protocol (MCP)**: Extensible tool system to create custom integrations
- **Multi-Model Support**: Works with OpenAI, Anthropic, Google Gemini, AWS Bedrock, Azure, local models
- **Workspace Snapshots**: Checkpoint system to roll back changes at any point
- **Token Usage Tracking**: Real-time cost monitoring across different API providers
- **Human-in-the-Loop**: Approval required for all file changes and terminal commands

## Installation/Setup
```bash
# Install via VS Code
1. Open VS Code
2. Press Ctrl+Shift+X (Cmd+Shift+X on Mac)
3. Search for "Cline"
4. Click Install on the extension by saoudrizwan

# Alternative: Command Palette
1. Press Ctrl+Shift+P (Cmd+Shift+P on Mac)
2. Type "Extensions: Install Extensions"
3. Search for "Cline" and install
```

## Basic Usage
```javascript
// Open Cline
1. Click the Cline icon in Activity Bar
2. Or use Ctrl+Shift+P → "Cline: Open In New Tab"

// Basic interaction
"Create a React component for a todo list"
"Debug the error in my Python script"
"Add a database connection to this Node.js app"
"Test the application and fix any visual bugs"

// Advanced tasks
"Convert this design mockup into a working app"
"Add a tool that fetches data from our API"
"Refactor this codebase to use TypeScript"
```

## Configuration
```json
// Extension Settings (accessible via gear icon)
{
  "apiProvider": "anthropic", // or "openai", "openrouter", etc.
  "apiKey": "your-api-key-here",
  "model": "claude-3-7-sonnet",
  "autoApproveReadonly": true,
  "maxTokens": 8192,
  "temperature": 0,
  "customInstructions": "Always explain your reasoning"
}

// Supported API Providers
- Anthropic (Claude)
- OpenAI (GPT models)
- Google Gemini
- OpenRouter (multiple models)
- AWS Bedrock
- Azure OpenAI
- Local models (via LM Studio/Ollama)
```

## Alternatives
- **Cursor**: VS Code fork with built-in AI, subscription-based with immediate code generation focus
- **GitHub Copilot**: Microsoft's AI assistant, great for autocomplete but less autonomous
- **Windsurf**: IDE with collaborative AI features, similar capabilities but closed-source
- **Aider**: Command-line AI coding assistant, terminal-based without IDE integration
- **Roo Code**: Fork of Cline with additional team collaboration features

## Pros/Cons
**Pros:**
- Completely open-source and free (pay only for API usage)
- Model-agnostic with support for multiple AI providers
- Autonomous task execution with human oversight
- Extensible through Model Context Protocol (MCP)
- Advanced browser automation and visual debugging
- Transparent token usage and cost tracking
- Active development and strong community support

**Cons:**
- Requires API keys and credits from AI providers (ongoing cost)
- Can be expensive with high token usage for complex tasks
- Limited by rate limits of chosen AI model provider
- Requires VS Code (not available as standalone application)
- Learning curve for optimal prompt engineering

## Date Added
2025-05-14

## Last Updated
2025-05-14

## Notes
- Cline was previously known as "ClaudeDev"
- Best performance achieved with Anthropic's Claude 3.7 Sonnet model
- MCP Marketplace (v3.4+) allows installation of community-created tools
- Workspace snapshots provide safe experimentation without losing progress
- Human approval required for all destructive actions, ensuring code safety
- Extremely active development with frequent feature releases
- Strong community on Discord and GitHub for support and contributions
- Consider using OpenRouter for cost optimization and access to multiple models