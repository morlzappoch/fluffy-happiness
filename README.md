#!/bin/bash

# ==========================================
# Sovereign Asset Protection - Auto-Deploy Script
# Owner: Morley Moses Apooch
# Repository: fluffy-happiness
# ==========================================

set -e # Exit immediately if a command exits with a non-zero status

echo "🚀 Starting Sovereign Asset Protection Deployment..."

# 1. Check if we are in the right directory
if [ ! -f "server.js" ]; then
    echo "❌ Error: server.js not found. Are you in the correct project directory?"
    exit 1
fi

# 2. Create .gitignore to protect secrets
echo "🛡️  Creating .gitignore..."
cat <<EOF > .gitignore
.env
node_modules
.DS_Store
*.log
EOF
echo "✅ .gitignore created."

# 3. Initialize Git if not already done
if [ ! -d ".git" ]; then
    echo "📦 Initializing Git repository..."
    git init
else
    echo "ℹ️  Git repository already initialized."
fi

# 4. Stage all changes
echo "📝 Staging all changes..."
git add .

# 5. Commit changes
echo "💾 Committing changes..."
git commit -m "feat: deploy Sovereign Asset Protection System with Admin Panel & Immutable Lock"

# 6. Set branch name (handles both 'main' and 'master')
BRANCH_NAME=$(git symbolic-ref HEAD 2>/dev/null | awk -F/ '{print $NF}')
if [ -z "$BRANCH_NAME" ]; then
    BRANCH_NAME="main"
    git branch -M main
    echo "ℹ️  Default branch set to: $BRANCH_NAME"
else
    echo "ℹ️  Current branch: $BRANCH_NAME"
fi

# 7. Link to remote repository
REMOTE_URL="https://github.com/morlzappoch/fluffy-happiness.git"
echo "🔗 Linking to remote: $REMOTE_URL"

# Check if origin exists
if git remote get-url origin > /dev/null 2>&1; then
    echo "ℹ️  Remote 'origin' already exists. Updating URL..."
    git remote set-url origin "$REMOTE_URL"
else
    echo "ℹ️  Adding remote 'origin'..."
    git remote add origin "$REMOTE_URL"
fi

# 8. Push to GitHub
echo "🚀 Pushing to GitHub..."
# Force push if it's a new repo or branch mismatch, otherwise standard push
git push -u origin $BRANCH_NAME --force

echo "✅ Deployment Complete!"
echo "🌐 Your code is now on GitHub. You can connect this repo to Render."
echo "🔒 Remember to set your ENV variables on Render: SECRET_KEY, ADMIN_USER, ADMIN_PASS_HASH"
