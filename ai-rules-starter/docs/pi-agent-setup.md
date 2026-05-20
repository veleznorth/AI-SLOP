# Pi Agent Setup

Pi Agent is the read-only harness for AI Rules planner and reader work. Use it
only to inspect repository context, search files, and produce evidence packets
that another agent or human can review.

Pi is not an implementer. It must not write code, edit files, run shell
commands, install dependencies, enable MCP, or keep session state by default.

## Default Command

```sh
pi --tools read,grep,find,ls --no-session
```

Use the default command for normal planner/reader tasks when extensions,
skills, prompt templates, and themes are already controlled by the surrounding
environment.

## Strict Command

```sh
pi --tools read,grep,find,ls --no-session --no-extensions --no-skills --no-prompt-templates --no-themes
```

Use the strict command when the task needs the smallest reproducible read-only
surface.

## Allowed Capabilities

- `read`: open specific files requested by the reading plan.
- `grep`: search text patterns in the allowed scope.
- `find`: discover files and directories in the allowed scope.
- `ls`: list directories in the allowed scope.

## Prohibited Capabilities

- Writing files.
- Editing files.
- Running bash or shell commands.
- Installing dependencies.
- Using MCP.
- Persisting a session by default.

## Stop Labels

Use these labels when Pi cannot continue safely as a read-only harness:

- `HARNESS_SCOPE_EXCEEDED`: the request requires access outside the allowed
  scope or asks Pi to act as an implementer.
- `HARNESS_INSUFFICIENT`: read-only tools are not enough to answer the planner
  or evidence request.
- `STOP-SCOPE-ESCALATION-REQUIRED`: continuing would require write, edit, bash,
  dependency installation, MCP, or persistent session behavior.
