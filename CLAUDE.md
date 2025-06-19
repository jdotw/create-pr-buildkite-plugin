This repo contains scripts that are executed by Buildkite to create pull requests from Linear issues.

Buildkite is a workflow automation platform that enables developers to automate their build, test, and deployment processes. It provides a powerful and flexible platform for building and deploying applications, and is widely used by organizations of all sizes.

There are two type of scripts in this repo; hooks (in 'hooks') and scripts in (in 'bin'). Hooks are run in the agent before the main command is run. Scripts are copied into the Docker image in which the main command is run. And in this case, the main command is actually 'bin/generate-pr'.

./hooks/environment is a script that ensures the scripts in ./bin can be accessed by the agent as it executes the pipeline steps.

./hooks/pre-comment is a script that runs immediately before the main command of the pipeline

./bin/generate-pr is the main script that runs in a Docker container created by the agent when running the pipeline. It is the primary purpose of this repo.

The generate-pr script examines environment variables, particularly those with a LINEAR_ prefix to form a Claude Code prompt that attempts to address the issue described in the Linear ticket.
