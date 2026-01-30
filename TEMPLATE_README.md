# Claude Project Template

A [Copier](https://copier.readthedocs.io/) template for quickly setting up new projects with Claude Code.

## Features

- Interactive project setup with sensible defaults
- Optional [bd (beads)](https://github.com/cased/bd) issue tracking integration
- Automatic GitHub repository creation
- Pre-configured `.gitignore` with common patterns
- CLAUDE.md for project-specific Claude Code instructions

## Prerequisites

- [Copier](https://copier.readthedocs.io/): `pip install --user copier`
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
