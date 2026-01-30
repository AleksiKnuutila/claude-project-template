# Copier Template Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Transform this repository into a working Copier template that creates new Claude Code projects with interactive configuration.

**Architecture:** Convert existing files to Jinja2 templates, add copier.yml configuration with interactive questions, and create post-generation hook script for git/GitHub/bd automation.

**Tech Stack:** Copier (Python templating tool), Jinja2, Bash, GitHub CLI, bd (beads)

---

## Task 1: Install Copier and Verify Setup

**Files:**
- None (environment setup)

**Step 1: Install Copier**

Run: `pipx install copier`
Expected: "installed package copier" or "copier is already installed"

**Step 2: Verify installation**

Run: `copier --version`
Expected: Version output (e.g., "9.x.x")

**Step 3: Verify gh CLI is installed**

Run: `gh --version`
Expected: Version output (e.g., "gh version 2.x.x")

---

## Task 2: Create copier.yml Configuration

**Files:**
- Create: `copier.yml`

**Step 1: Create copier.yml with questions**

```yaml
# Copier configuration
_templates_suffix: .jinja
_answers_file: .copier-answers.yml

# Questions
project_name:
  type: str
  help: "Project name (lowercase-with-dashes)"
  validator: >-
    {% if not project_name.replace('-', '').replace('_', '').isalnum() %}
    Must contain only lowercase letters, numbers, dashes, and underscores
    {% endif %}

project_description:
  type: str
  help: "Brief project description"
  default: ""

github_username:
  type: str
  help: "GitHub username (for repo creation)"

use_bd:
  type: bool
  help: "Use bd (beads) for issue tracking?"
  default: yes

create_github_repo:
  type: bool
  help: "Create private GitHub repository?"
  default: yes

# Computed variables
_project_slug:
  type: str
  default: "{{ project_name | lower | replace(' ', '-') }}"

_current_year:
  type: str
  default: "2026"

# Post-generation tasks
_tasks:
  - "chmod +x .copier-post-gen.sh"
  - "bash .copier-post-gen.sh"
  - "rm .copier-post-gen.sh"
```

**Step 2: Commit copier.yml**

Run:
```bash
git add copier.yml
git commit -m "Add copier.yml configuration

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 3: Convert CLAUDE.md to Template

**Files:**
- Rename: `CLAUDE.md` to `CLAUDE.md.jinja`
- Modify: `CLAUDE.md.jinja` (entire file)

**Step 1: Rename file**

Run: `git mv CLAUDE.md CLAUDE.md.jinja`

**Step 2: Replace content with template**

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

**Step 3: Commit template**

Run:
```bash
git add CLAUDE.md.jinja
git commit -m "Convert CLAUDE.md to Jinja2 template

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 4: Create .gitignore Template

**Files:**
- Rename: `.gitignore` to `.gitignore.jinja`
- Modify: `.gitignore.jinja` (entire file)

**Step 1: Rename file**

Run: `git mv .gitignore .gitignore.jinja`

**Step 2: Replace content with template**

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
dist/
build/

# Copier
.copier-answers.yml
```

**Step 3: Commit template**

Run:
```bash
git add .gitignore.jinja
git commit -m "Convert .gitignore to Jinja2 template

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 5: Create README Template

**Files:**
- Create: `README.md.jinja`

**Step 1: Create README template**

```markdown
# {{ project_name }}

{% if project_description %}{{ project_description }}{% endif %}

## Setup

[Add setup instructions here]

## Development

[Add development workflow here]

{% if use_bd %}
## Issue Tracking

This project uses [bd (beads)](https://github.com/cased/bd) for issue tracking.

Quick reference:
- `bd ready` - Find unblocked work
- `bd create "Title"` - Create new issue
- `bd close <id>` - Complete work
{% endif %}
```

**Step 2: Commit README template**

Run:
```bash
git add README.md.jinja
git commit -m "Add README.md template

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 6: Create Post-Generation Hook Script

**Files:**
- Create: `.copier-post-gen.sh`

**Step 1: Create hook script**

```bash
#!/bin/bash
set -e

echo "🚀 Setting up {{ project_name }}..."

# Initialize git
git init
git add .
git commit -m "Initial commit from claude-project-template

Project: {{ project_name }}
{% if project_description %}Description: {{ project_description }}{% endif %}

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"

{% if create_github_repo %}
# Create GitHub repo if requested
if command -v gh &> /dev/null; then
  echo "📦 Creating private GitHub repository..."
  if gh repo create "{{ github_username }}/{{ project_name }}" \
    --private \
    --source=. \
    --remote=origin \
    --push; then
    echo "✅ Repository created: https://github.com/{{ github_username }}/{{ project_name }}"
  else
    echo "⚠️  Failed to create GitHub repository. You can create it manually later."
  fi
else
  echo "⚠️  gh CLI not found. Skipping GitHub repo creation."
  echo "   Install with: sudo apt install gh"
  echo "   Then run: gh auth login"
fi
{% else %}
echo "ℹ️  Skipping GitHub repository creation (not requested)"
{% endif %}

{% if use_bd %}
# Initialize bd if requested
if command -v bd &> /dev/null; then
  echo "📋 Initializing bd (beads) issue tracking..."
  bd init
  echo "✅ bd initialized. Run 'bd prime' for workflow context."
else
  echo "⚠️  bd not found. Skipping bd initialization."
  echo "   Install from: https://github.com/cased/bd"
fi
{% else %}
echo "ℹ️  Skipping bd initialization (not requested)"
{% endif %}

echo ""
echo "✅ Project setup complete!"
echo ""
echo "Next steps:"
{% if create_github_repo %}
echo "  - cd {{ project_name }}"
{% if use_bd %}
echo "  - bd prime  # Learn the bd workflow"
{% endif %}
echo "  - Start coding!"
{% else %}
echo "  - cd {{ project_name }}"
echo "  - git remote add origin <your-repo-url>"
echo "  - git push -u origin master"
{% endif %}
```

**Step 2: Make script executable**

Run: `chmod +x .copier-post-gen.sh`

**Step 3: Commit hook script**

Run:
```bash
git add .copier-post-gen.sh
git commit -m "Add post-generation hook script

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 7: Create Template README

**Files:**
- Create: `TEMPLATE_README.md`

**Step 1: Create documentation for template usage**

```markdown
# Claude Project Template

A [Copier](https://copier.readthedocs.io/) template for quickly setting up new projects with Claude Code.

## Features

- Interactive project setup with sensible defaults
- Optional [bd (beads)](https://github.com/cased/bd) issue tracking integration
- Automatic GitHub repository creation
- Pre-configured `.gitignore` with common patterns
- CLAUDE.md for project-specific Claude Code instructions

## Prerequisites

- [Copier](https://copier.readthedocs.io/): `pipx install copier`
- [GitHub CLI](https://cli.github.com/) (optional, for repo creation): `sudo apt install gh`
- [bd (beads)](https://github.com/cased/bd) (optional, for issue tracking)

## Usage

### Create New Project

```bash
copier copy gh:AleksiKnuutila/claude-project-template my-new-project
cd my-new-project
```

You'll be asked:
1. Project name (lowercase-with-dashes)
2. Brief description
3. GitHub username
4. Use bd for issue tracking? (Y/n)
5. Create private GitHub repo? (Y/n)

The template will:
- Initialize git repository
- Create initial commit
- Create private GitHub repo (if requested)
- Initialize bd (if requested)
- Push to GitHub (if repo created)

### Update Existing Project

If the template improves, you can update your project:

```bash
cd my-project
copier update
```

### Test Template Locally

Before using, you can test it:

```bash
copier copy /path/to/claude-project-template /tmp/test-project
```

## What You Get

```
my-new-project/
├── .gitignore           # Ignores CLAUDE.md, common patterns
├── CLAUDE.md            # Project-specific Claude Code instructions
└── README.md            # Project README
```

Plus git initialization and optional GitHub repo.

## Customization

The template uses Jinja2. Files ending in `.jinja` are processed:

- `CLAUDE.md.jinja` - Conditional bd instructions
- `.gitignore.jinja` - Standard ignore patterns
- `README.md.jinja` - Basic project README
- `.copier-post-gen.sh` - Setup automation

## License

Public domain / CC0
```

**Step 2: Commit template documentation**

Run:
```bash
git add TEMPLATE_README.md
git commit -m "Add template usage documentation

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 8: Test Template Locally

**Files:**
- None (testing)

**Step 1: Test template generation**

Run:
```bash
cd /tmp
copier copy /home/aleksi/Projects/claude-project-template/.worktrees/implement-copier test-project-1
```

Provide test answers:
- project_name: `test-project`
- project_description: `A test project`
- github_username: `testuser`
- use_bd: `no`
- create_github_repo: `no`

Expected: Project created successfully

**Step 2: Verify generated files**

Run:
```bash
ls -la /tmp/test-project-1/
cat /tmp/test-project-1/CLAUDE.md
cat /tmp/test-project-1/.gitignore
cat /tmp/test-project-1/README.md
```

Expected:
- CLAUDE.md contains non-bd template (project name, commented examples)
- .gitignore contains CLAUDE.md and common patterns
- README.md contains project name and description
- Git is initialized
- No GitHub repo created
- No bd initialized

**Step 3: Test with all features enabled**

Run:
```bash
cd /tmp
copier copy /home/aleksi/Projects/claude-project-template/.worktrees/implement-copier test-project-2
```

Provide test answers:
- project_name: `test-project-2`
- project_description: `Another test`
- github_username: `testuser`
- use_bd: `yes`
- create_github_repo: `no` (avoid creating real repos)

Expected: Project created with bd instructions in CLAUDE.md

**Step 4: Verify bd template**

Run: `cat /tmp/test-project-2/CLAUDE.md`

Expected: Contains bd issue tracking instructions

**Step 5: Clean up test projects**

Run: `rm -rf /tmp/test-project-1 /tmp/test-project-2`

---

## Task 9: Merge to Main and Push

**Files:**
- None (git workflow)

**Step 1: Push feature branch**

Run:
```bash
git push -u origin feature/implement-copier
```

Expected: Branch pushed to GitHub

**Step 2: Switch to main branch**

Run:
```bash
cd /home/aleksi/Projects/claude-project-template
git checkout master
```

**Step 3: Merge feature branch**

Run:
```bash
git merge feature/implement-copier --no-ff -m "Implement Copier template

- Add copier.yml configuration
- Convert files to Jinja2 templates
- Add post-generation hook script
- Add template documentation

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

**Step 4: Push to origin**

Run: `git push origin master`

**Step 5: Clean up worktree**

Run:
```bash
git worktree remove .worktrees/implement-copier
git branch -d feature/implement-copier
```

---

## Success Criteria

- [ ] Template generates valid projects
- [ ] CLAUDE.md is conditional based on use_bd
- [ ] .gitignore includes CLAUDE.md and common patterns
- [ ] Post-generation hook handles git/GitHub/bd setup
- [ ] GitHub repo creation works (when gh CLI available)
- [ ] bd initialization works (when bd available)
- [ ] Template documentation is clear
- [ ] All changes committed and pushed to master
