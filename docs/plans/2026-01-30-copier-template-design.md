# Copier Template Design

**Date:** 2026-01-30
**Status:** Approved

## Overview

Transform this repository into a Copier template for creating new Claude Code projects. The template will be minimal and flexible, providing essential starter files with interactive configuration.

## Architecture

### Template Repository Structure

```
claude-project-template/
├── copier.yml                    # Copier configuration with questions
├── CLAUDE.md.jinja              # Template for CLAUDE.md
├── .gitignore.jinja             # Template for .gitignore
├── README.md.jinja              # Template for README.md
├── .copier-post-gen.sh          # Post-generation automation script
└── .copier-answers.yml          # Tracks user's answers for updates
```

### How It Works

1. User runs: `copier copy gh:yourusername/claude-project-template my-new-project`
2. Copier asks interactive questions (project name, author, features)
3. Jinja2 templates render with user's answers
4. Post-generation hook script runs to handle automation
5. User gets a ready-to-use project directory

**Key Design Decision:** Using Jinja2 templating (.jinja extension) allows conditional content in CLAUDE.md and .gitignore based on user's choices. The `.copier-answers.yml` file lets users update their project later if the template improves.

## Interactive Questions & Variables

### Questions (copier.yml)

**Basic Information:**
```yaml
project_name:
  type: str
  help: Project name (lowercase-with-dashes)

project_description:
  type: str
  help: Brief project description
  default: ""

github_username:
  type: str
  help: GitHub username (for repo creation)
```

**Feature Toggles:**
```yaml
use_bd:
  type: bool
  help: Use bd (beads) for issue tracking?
  default: yes

create_github_repo:
  type: bool
  help: Create private GitHub repository?
  default: yes
```

**Internal Variables** (not asked, auto-set):
- `project_slug`: sanitized version of project_name
- `current_year`: current year for potential future use

Total: 5 interactive questions

## Template Files

### CLAUDE.md.jinja

```jinja
{% if use_bd %}
## Issue Tracking

This project uses **bd (beads)** for issue tracking.
Run `bd prime` for workflow context, or install hooks (`bd hooks install`) for auto-injection.

**Quick reference:**
- `bd ready` - Find unblocked work
- `bd create "Title" --type task --priority 2` - Create issue
- `bd close <id>` - Complete work
- `bd sync` - Sync with git (run at session end)

For full workflow details: `bd prime`
{% else %}
## Project: {{ project_name }}

{% if project_description %}{{ project_description }}{% endif %}

<!-- Add project-specific Claude Code instructions here -->
<!-- Examples:
- Code style preferences
- Testing requirements
- Architectural decisions
- Deployment notes
-->
{% endif %}
```

### .gitignore.jinja

```jinja
# Claude Code
CLAUDE.md
CLAUDE.local.md

# Common patterns
.DS_Store
.env
.env.local
*.log

# IDEs
.vscode/
.idea/
*.swp
*.swo

# Dependencies (common across languages)
node_modules/
__pycache__/
*.pyc
vendor/
target/
```

### README.md.jinja

```markdown
# {{ project_name }}

{{ project_description }}

## Setup

[Add setup instructions]
```

## Post-Generation Hook

### copier.yml _tasks section

```yaml
_tasks:
  - "bash .copier-post-gen.sh"
```

### .copier-post-gen.sh

```bash
#!/bin/bash
set -e

echo "🚀 Setting up project..."

# Initialize git
git init
git add .
git commit -m "Initial commit from claude-project-template

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"

# Create GitHub repo if requested
{% if create_github_repo %}
if command -v gh &> /dev/null; then
  echo "📦 Creating GitHub repository..."
  gh repo create "{{ github_username }}/{{ project_name }}" \
    --private \
    --source=. \
    --remote=origin \
    --push
else
  echo "⚠️  gh CLI not found. Skipping GitHub repo creation."
  echo "   Install: sudo apt install gh"
fi
{% endif %}

# Initialize bd if requested
{% if use_bd %}
if command -v bd &> /dev/null; then
  echo "📋 Initializing bd..."
  bd init
else
  echo "⚠️  bd not found. Skipping bd init."
  echo "   Install: https://github.com/cased/bd"
fi
{% endif %}

echo "✅ Project setup complete!"
```

**Features:**
- Checks for tool availability before running commands
- Provides helpful warnings if tools are missing
- Handles full automation flow: git init, GitHub repo, bd init, commit, push

## Setup & Usage

### Setting Up Template Repository

1. **Install Copier:**
   ```bash
   pipx install copier
   ```

2. **Initialize this repo with Copier structure:**
   ```bash
   # Rename files to .jinja templates
   mv CLAUDE.md CLAUDE.md.jinja

   # Create copier.yml with questions
   # Create .copier-post-gen.sh with hook script
   # Create .gitignore.jinja and README.md.jinja
   ```

3. **Commit template to GitHub:**
   ```bash
   git init
   git add .
   git commit -m "Initial Copier template"
   gh repo create yourusername/claude-project-template --public --source=. --push
   ```

### Using the Template

```bash
# Create new project
copier copy gh:yourusername/claude-project-template my-new-project
cd my-new-project
# Answer questions interactively
# Project is ready!
```

### Updating Projects Later

If the template improves, existing projects can pull updates:
```bash
cd existing-project
copier update
# Merges template changes
```

### Testing Locally

Before publishing:
```bash
copier copy /path/to/claude-project-template /tmp/test-project
```

## Dependencies

**Required:**
- copier (Python package)

**Optional (checked by post-gen hook):**
- gh (GitHub CLI) - for repo creation
- bd (beads) - for issue tracking

## Key Benefits

1. **Fast project setup** - One command creates configured project
2. **Consistent structure** - All projects follow same pattern
3. **Flexible** - Works across any tech stack
4. **Updateable** - Projects can pull template improvements
5. **Automated** - Git, GitHub, and bd setup handled automatically
