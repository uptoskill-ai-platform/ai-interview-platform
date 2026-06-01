# Contributing Guide

## Before You Start

1. Clone the repository

```bash
git clone https://github.com/uptoskill-ai-platform/ai-interview-platform.git
cd ai-interview-platform
```

2. Set up your environment file

3. Fill in the required values in your `.env` file.
   Never commit your `.env` file.

---

## Git Workflow

### Important Rules

- Never push directly to `main`
- Every change must go through a Pull Request
- One branch per task
- Keep your branch up to date with main

### Step by Step

```bash
# Always start from the latest main
git checkout main
git pull origin main

# Create your feature branch
git checkout -b feature/your-task-name

# Make your changes, then stage and commit
git add .
git commit -m "feat: brief description of what you built"

# Push your branch
git push origin feature/your-task-name

# Go to GitHub and open a Pull Request
# Request review from the captain
```

### Branch Naming
feature/short-description-of-task
fix/what-you-fixed
docs/what-you-documented

### Commit Message Format
feat: add new feature
fix: correct a bug
docs: update documentation
test: add or update tests
refactor: restructure code without changing behavior

---

## Before Raising a Pull Request

Make sure all of the following are true:

- [ ] Code runs locally without errors
- [ ] No `.env` file is committed
- [ ] No API keys or passwords hardcoded in code
- [ ] Pull Request has a clear title and description
- [ ] Review has been requested from the captain

---

## Need Help?

Post your question in the team group chat.
Do not wait silently if you are stuck — raise it early.