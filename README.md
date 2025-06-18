# Create PR Buildkite Plugin

A Buildkite plugin that automatically creates pull requests to solve Linear issues using Claude Code CLI. This plugin integrates with Linear to extract full issue context including comments, history, and metadata.

## Example

```yml
steps:
  - label: ":linear: Create PR from Linear Issue"
    plugins:
      - create-pr#v1.0.0
```

## Environment Variables

The plugin requires the following environment variables to be set:

### Required

- `LINEAR_ISSUE_IDENTIFIER` - The Linear issue ID (e.g., "PROJ-123")
- `LINEAR_ISSUE_TITLE` - Title of the Linear issue
- `LINEAR_ISSUE_DESCRIPTION` - Description of the Linear issue
- `LINEAR_ISSUE_URL` - URL to the Linear issue
- `ISSUE_DATA_JSON` - Full JSON data of the Linear issue including history and comments
- `GITHUB_TOKEN` - GitHub token with repo access for creating PRs
- `USER_NAME` - Name of the user triggering the build
- `USER_EMAIL` - Email of the user triggering the build

### Optional

- `LINEAR_TEAM_NAME` - Linear team name
- `LINEAR_ASSIGNEE` - Issue assignee
- `LINEAR_ISSUE_PRIORITY` - Issue priority
- `LINEAR_ISSUE_STATE` - Current issue state
- `LINEAR_CREATOR` - Issue creator
- `DEBUG_SKIP_CLAUDE` - Set to skip Claude Code invocation (for testing)

## How it works

1. **Issue Context Extraction**: The plugin parses the Linear issue JSON data to extract:
   - Issue details (title, description, URL, priority, state)
   - Comment history from team members
   - State change history
   - Assignee changes

2. **Git Setup**: 
   - Configures git authentication using GITHUB_TOKEN
   - Creates a unique branch named `linear-{issue-id}` (with counter if exists)

3. **Claude Code Integration**:
   - Creates a comprehensive context file with all issue information
   - Invokes Claude Code CLI with a Rails-specific prompt
   - Falls back to npx if claude-code isn't in PATH
   - Uses the Sonnet model for code generation

4. **PR Creation**:
   - Commits changes with detailed commit message
   - Creates a GitHub PR with full Linear issue context
   - Stores PR metadata in Buildkite for downstream steps

## Requirements

- `claude-code` CLI or `@anthropic-ai/claude-code` npm package
- `jq` for JSON parsing
- `gh` (GitHub CLI) for PR creation
- Git repository with push access
- Valid GITHUB_TOKEN with repo permissions

## Rails Integration

This plugin is optimized for Rails applications and instructs Claude Code to:
- Follow Rails conventions and best practices
- Create appropriate controllers, models, views, and routes
- Add tests when applicable
- Match existing codebase patterns

## Testing Mode

Set `DEBUG_SKIP_CLAUDE=1` to skip Claude Code invocation and create a mock change file instead. Useful for testing the pipeline without consuming Claude API credits.

## Example Pipeline with Linear Webhook

```yml
steps:
  - label: ":webhook: Process Linear Webhook"
    command: |
      # Parse Linear webhook data
      export LINEAR_ISSUE_IDENTIFIER=$(echo "$WEBHOOK_DATA" | jq -r '.issue.identifier')
      export LINEAR_ISSUE_TITLE=$(echo "$WEBHOOK_DATA" | jq -r '.issue.title')
      # ... set other required variables
    
  - wait

  - label: ":linear: Create PR from Linear Issue"
    plugins:
      - create-pr#v1.0.0

  - wait

  - label: ":github: Comment on Linear"
    command: |
      PR_URL=$(buildkite-agent meta-data get "pull_request_url")
      # Post PR URL back to Linear issue
```

## Debugging

The plugin provides detailed logging at each step:
- Issue parsing results
- Branch creation process
- Claude Code availability check
- Changes detection
- PR creation details

Check the Buildkite build logs for troubleshooting any issues.