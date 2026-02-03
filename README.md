# Everything Claude Code

Complete Claude Code configuration collection - agents, skills, hooks, commands, rules, MCPs. Battle-tested configs from an Anthropic hackathon winner.

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/affaan-m/everything-claude-code.git
cd everything-claude-code

# Install as Claude Code plugin
# Add to marketplace
/plugin marketplace add affaan-m/everything-claude-code

# Install plugin
/plugin install everything-claude-code@everything-claude-code
```

### Install Rules (Required)

⚠️ **Important**: Claude Code plugins cannot distribute rules automatically. Install them manually:

```bash
# Copy rules to your Claude config directory
cp -r rules/* ~/.claude/rules/
```

### Start Using

```bash
# Try a command
/plan "Add user authentication"

# Check available commands
/plugin list everything-claude-code@everything-claude-code
```

## 📦 What's Inside

### Agents (11)
Specialized subagents for delegation:
- **planner.md** - Feature implementation planning
- **architect.md** - System design decisions
- **tdd-guide.md** - Test-driven development
- **code-reviewer.md** - Quality and security review
- **security-reviewer.md** - Vulnerability analysis
- **build-error-resolver.md** - Build error resolution
- **e2e-runner.md** - Playwright E2E testing
- **refactor-cleaner.md** - Dead code cleanup
- **doc-updater.md** - Documentation sync
- **go-reviewer.md** - Go code review
- **go-build-resolver.md** - Go build error resolution

### Skills (13)
Workflow definitions and domain knowledge:
- **coding-standards/** - Language best practices
- **backend-patterns/** - API, database, caching patterns
- **frontend-patterns/** - React, Next.js patterns
- **tdd-workflow/** - TDD methodology
- **security-review/** - Security checklist
- **golang-patterns/** - Go idioms and best practices
- **golang-testing/** - Go testing patterns, TDD, benchmarks
- And more...

### Commands (18)
Slash commands for quick execution:
- **/plan** - Implementation planning
- **/tdd** - Test-driven development
- **/e2e** - E2E test generation
- **/code-review** - Quality review
- **/build-fix** - Fix build errors
- **/refactor-clean** - Dead code removal
- **/go-review** - Go code review
- **/go-test** - Go TDD workflow
- **/go-build** - Fix Go build errors
- And more...

### Rules (6)
Always-follow guidelines:
- **security.md** - Mandatory security checks
- **coding-style.md** - Immutability, file organization
- **testing.md** - TDD, 80% coverage requirement
- **git-workflow.md** - Commit format, PR process
- **agents.md** - When to delegate to subagents
- **performance.md** - Model selection, context management

## 🎯 Key Features

### Production-Ready
- Battle-tested over 10+ months of intensive daily use
- Used to build real products
- Evolved from Anthropic hackathon winner's workflow

### Comprehensive
- 15+ specialized agents
- 30+ skills and patterns
- 20+ slash commands
- Complete security and testing guidelines

### Cross-Platform
- Works on Windows, macOS, Linux
- Automatic package manager detection
- Platform-specific optimizations

## 🌐 Cross-Platform Support

The plugin automatically detects and works with:
- **npm/yarn/pnpm** (Node.js)
- **pip/poetry** (Python)
- **go mod** (Go)
- **maven/gradle** (Java)

## 📋 Requirements

- Claude Code CLI version 0.6.0 or higher
- Git (for installation)

## 🛠️ Usage Examples

### Planning a Feature
```bash
/plan "Add user authentication with OAuth2"
```

### Test-Driven Development
```bash
/tdd "Create payment processing service"
```

### Code Review
```bash
/code-review --file=src/userService.js
```

### Security Review
```bash
# Security review will be triggered automatically
# Or manually review specific files
```

### Go Development
```bash
/go-review --package=./internal/auth
/go-test "Create user repository"
/go-build
```

## 🤝 Contributing

Contributions are welcome! Please read the contributing guidelines before submitting PRs.

### Ideas for Contributions
- New agents for specialized tasks
- Additional language-specific patterns
- More comprehensive skills
- Additional slash commands
- Documentation improvements

## 📖 Background

This collection represents real-world configurations evolved over months of intensive development. Every agent, skill, and command has been refined through actual use in building production applications.

## ⚠️ Important Notes

### Context Window Management
- Agents are designed to work within Claude's context limits
- Skills use progressive disclosure patterns
- Commands are optimized for quick execution

### Customization
All components can be customized to fit your workflow:
- Modify agents for your specific needs
- Add custom skills for your domain
- Create new commands for common tasks
- Adjust rules to match your team's standards

## 📄 License

MIT License - see LICENSE file for details

## 🔗 Links

- [GitHub Repository](https://github.com/affaan-m/everything-claude-code)
- [Documentation](https://github.com/affaan-m/everything-claude-code/wiki)
- [Issue Tracker](https://github.com/affaan-m/everything-claude-code/issues)

---

**Note**: This is a comprehensive configuration collection. Start with the basics and gradually adopt more components as needed. Not every project needs every agent or skill.

✨ **Happy Coding with Claude!**
