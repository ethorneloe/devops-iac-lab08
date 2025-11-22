# Lab 08: Branch Protection & Pull Request Workflow

## Overview

This lab teaches Git branching discipline, pull request (PR) workflow, and required status checks. You'll learn how to protect your main branch from direct pushes and ensure all changes go through a review process with automated checks.

**Key Learning Objectives:**
- Understand branch protection rules
- Practice feature branch workflow
- Work with pull requests
- Implement required status checks
- Use PR templates and code owners

## Prerequisites

- Git installed and configured
- GitHub account
- Basic understanding of Git commands
- Familiarity with GitHub workflows

## Repository Structure

```
08-branch-protection-and-pr-workflow/
├── README.md                          # This file
├── .gitignore                         # Ignored files configuration
├── .github/
│   ├── workflows/
│   │   └── basic-checks.yml          # CI workflow for PR checks
│   ├── pull_request_template.md      # PR template
│   └── CODEOWNERS                     # Code ownership configuration
└── src/
    └── app.txt                        # Simple application file
```

## Part 1: Instructor/Tutor Setup (One-Time)

### Step 1: Create the Official Repository

1. Create a new repository from this template or fork it
2. This will be the "official" starter repository for students

### Step 2: Configure Branch Protection Rules

Navigate to your repository on GitHub and set up branch protection:

1. Go to **Settings** → **Branches**
2. Click **Add branch protection rule**
3. Configure the following settings:

   **Branch name pattern:**
   ```
   main
   ```

   **Protection settings to enable:**
   - ✅ **Require a pull request before merging**
     - ✅ Require approvals (optional, set to 1 or more)
     - ✅ Dismiss stale pull request approvals when new commits are pushed

   - ✅ **Require status checks to pass before merging**
     - ✅ Require branches to be up to date before merging
     - Search and select: `Run Basic Checks` (this is the CI workflow)

   - ✅ **Require conversation resolution before merging** (optional but recommended)

   - ✅ **Include administrators** (optional, makes rules apply to everyone)

   - ✅ **Require code owner reviews** (if using CODEOWNERS file)

4. Click **Create** or **Save changes**

### Step 3: Update CODEOWNERS

Edit `.github/CODEOWNERS` and replace `@your-github-handle` with your actual GitHub username:

```
*       @your-actual-username
/src/   @your-actual-username
```

### Step 4: Verify Workflow

1. Ensure the `.github/workflows/basic-checks.yml` file exists
2. The workflow will automatically run on pull requests to `main`
3. It checks that `src/app.txt` exists and is not empty

## Part 2: Student Workflow

### Step 1: Get Your Own Copy

Choose one of the following methods:

**Option A: Fork the repository**
```bash
# Fork via GitHub UI, then clone your fork
git clone https://github.com/YOUR-USERNAME/08-branch-protection-and-pr-workflow.git
cd 08-branch-protection-and-pr-workflow
```

**Option B: Use as template**
```bash
# Create a new repo from template via GitHub UI, then clone
git clone https://github.com/YOUR-USERNAME/your-repo-name.git
cd your-repo-name
```

### Step 2: Create a Feature Branch

**Never work directly on the main branch!**

```bash
# Create and switch to a new feature branch
git checkout -b feature/update-app-text

# Verify you're on the feature branch
git branch
```

### Step 3: Make Your Changes

Edit the `src/app.txt` file:

```bash
# Open the file in your editor
nano src/app.txt  # or vim, code, etc.
```

Example change:
```
Version 2 - Updated message from [Your Name]

This file has been updated as part of Lab 08 to demonstrate:
- Feature branch workflow
- Pull request process
- Automated CI checks
- Branch protection in action
```

### Step 4: Commit Your Changes

```bash
# Stage the changes
git add src/app.txt

# Commit with a descriptive message
git commit -m "Update app message to version 2"

# Verify your commit
git log -1
```

### Step 5: Push Your Feature Branch

```bash
# Push the feature branch to GitHub
git push -u origin feature/update-app-text
```

### Step 6: Create a Pull Request

1. Go to your repository on GitHub
2. You should see a prompt: **"Compare & pull request"** - click it
   - Or go to **Pull requests** → **New pull request**
3. Set the base repository and branch (usually `main`)
4. Set the compare branch to your feature branch (`feature/update-app-text`)
5. Fill out the PR template that appears automatically:
   - Summary of change
   - Type of change
   - How to test
   - Complete the checklist
6. Click **Create pull request**

### Step 7: Observe the CI Checks

After creating the PR:

1. Watch the **Checks** tab on your PR
2. The `basic-checks` workflow will run automatically
3. You'll see:
   - ✅ Green checkmark if all checks pass
   - ❌ Red X if checks fail
4. **You cannot merge until checks pass!**

Common check failures:
- Empty `src/app.txt` file
- Missing `src/app.txt` file
- File contains only whitespace

### Step 8: Review and Merge

1. If code owner review is required, wait for approval
2. Address any review comments by:
   ```bash
   # Make changes
   git add .
   git commit -m "Address review comments"
   git push
   ```
3. Once all checks are green and reviews approved:
   - Click **Merge pull request**
   - Choose merge strategy (usually "Create a merge commit")
   - Click **Confirm merge**
4. Optionally delete the feature branch after merging

### Step 9: Sync Your Local Main Branch

```bash
# Switch back to main
git checkout main

# Pull the latest changes (including your merged PR)
git pull origin main

# Verify the changes
cat src/app.txt

# Clean up local feature branch (optional)
git branch -d feature/update-app-text
```

## Part 3: Testing Branch Protection

### Attempt a Direct Push to Main (This Should Fail!)

Try to push directly to main to see branch protection in action:

```bash
# Switch to main branch
git checkout main

# Make a change
echo "Direct push attempt" >> src/app.txt

# Commit
git commit -am "Attempting direct push to main"

# Try to push (this will be rejected!)
git push origin main
```

**Expected result:**
```
remote: error: GH006: Protected branch update failed for refs/heads/main.
remote: error: Changes must be made through a pull request.
```

If you see this error - great! Branch protection is working correctly.

**To undo the local commit:**
```bash
git reset --hard origin/main
```

## Understanding the CI Workflow

The `.github/workflows/basic-checks.yml` file defines automated checks that run on every PR:

**What it does:**
1. Triggers on pull requests to `main` branch
2. Runs on Ubuntu latest
3. Checks that `src/app.txt` exists
4. Verifies the file is not empty
5. Ensures the file contains meaningful content (not just whitespace)
6. Displays the current content

**Why it matters:**
- Prevents merging broken changes
- Enforces quality standards
- Can be extended with additional checks (linting, testing, etc.)

## Key Concepts

### Branch Protection Rules
- Prevent direct pushes to important branches
- Enforce code review process
- Require automated checks to pass
- Maintain code quality and stability

### Pull Request Workflow
1. Create feature branch
2. Make changes
3. Push branch
4. Open PR
5. CI checks run automatically
6. Code review (if required)
7. Merge when approved and checks pass

### Required Status Checks
- Automated tests/checks that must pass
- Defined in GitHub Actions workflows
- Marked as required in branch protection settings
- Block merging if they fail

### CODEOWNERS
- Defines who owns specific parts of the codebase
- Automatically requests reviews from owners
- Ensures the right people review changes
- Can be required via branch protection

## Common Issues and Solutions

### Issue: CI Checks Not Running

**Solution:**
- Ensure `.github/workflows/basic-checks.yml` exists
- Check that the workflow triggers on `pull_request` events
- Verify the workflow is enabled in Settings → Actions

### Issue: Can't Merge PR

**Possible causes:**
- CI checks are failing → Fix the code and push again
- Required reviews not approved → Request review from code owners
- Branch is out of date → Update branch with latest main
- Merge conflicts → Resolve conflicts locally and push

### Issue: "Protected Branch Update Failed"

**This is expected!** It means branch protection is working.
- Don't push directly to `main`
- Always create a feature branch and PR

## Extensions and Advanced Topics

### Add More Checks

Extend `.github/workflows/basic-checks.yml` with:
- Linting checks
- Unit tests
- Security scans
- Code formatting checks
- Terraform validation (`terraform fmt -check`, `terraform validate`)

### Multiple Reviewers

Configure branch protection to require reviews from multiple people or teams.

### Auto-Merge

Use GitHub's auto-merge feature to automatically merge PRs once checks pass and approvals are received.

### Draft PRs

Create draft PRs for work-in-progress changes that aren't ready for review yet.

## Best Practices

1. **Always create feature branches** - Never work directly on `main`
2. **Use descriptive branch names** - e.g., `feature/add-authentication`, `bugfix/fix-login-error`
3. **Write clear commit messages** - Explain what and why, not just how
4. **Keep PRs focused** - One feature/fix per PR
5. **Review your own changes** - Before requesting review from others
6. **Respond to feedback** - Address all review comments
7. **Keep branches up to date** - Regularly merge or rebase with main
8. **Delete merged branches** - Keep repository clean

## Additional Resources

- [GitHub Branch Protection Rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [About Pull Requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)
- [About Code Owners](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners)

## Lab Completion Checklist

- [ ] Successfully created a feature branch
- [ ] Modified `src/app.txt` with meaningful content
- [ ] Committed changes with a clear message
- [ ] Pushed feature branch to GitHub
- [ ] Created a pull request
- [ ] Observed CI checks running
- [ ] Waited for checks to pass (green checkmark)
- [ ] Successfully merged PR
- [ ] Synced local main branch with merged changes
- [ ] Attempted direct push to main (and saw it fail)
- [ ] Understand why branch protection is important

## Conclusion

Branch protection and PR workflows are essential practices in professional software development. They:
- Prevent accidental breakage of important branches
- Enforce code review and collaboration
- Maintain code quality through automated checks
- Create an audit trail of all changes
- Enable team collaboration at scale

Mastering these workflows will prepare you for real-world DevOps and software development environments.
