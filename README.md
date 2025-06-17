# Create PR Buildkite Plugin

A Buildkite plugin that automatically creates pull requests to solve Linear issues using Claude Code.

## Example

```yml
steps:
  - label: ":linear: Create PR from Linear Issue"
    env:
      LINEAR_ISSUE_TITLE: "Fix authentication bug"
      LINEAR_ISSUE_DESCRIPTION: "Users can't login when session expires"
    plugins:
      - create-pr#v1.0.0:
          base_branch: main
```

## Configuration

### Required

Either set via plugin configuration or environment variables:

- `linear_issue_title` (or `LINEAR_ISSUE_TITLE`) - Title of the Linear issue
- `linear_issue_description` (or `LINEAR_ISSUE_DESCRIPTION`) - Description of the Linear issue

### Optional

- `base_branch` (default: `main`) - Base branch for the PR
- `claude_code_flags` - Additional flags to pass to Claude Code

## How it works

1. The plugin validates that Linear issue title and description are provided
2. Creates a new branch from the specified base branch
3. Runs Claude Code with a prompt to solve the Linear issue
4. Claude Code analyzes the issue, implements the solution, and creates a PR
5. The branch is pushed to the remote repository

## Requirements

- `claude-code` CLI must be installed and configured
- Git repository with push access
- GitHub CLI (`gh`) configured for PR creation

## Testing

```bash
# Set environment variables
export LINEAR_ISSUE_TITLE="Add user profile page"
export LINEAR_ISSUE_DESCRIPTION="Create a user profile page with avatar and bio"

# Run the plugin hook directly
./hooks/command
```