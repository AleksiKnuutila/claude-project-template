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
