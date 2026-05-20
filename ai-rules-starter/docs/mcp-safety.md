# MCP Safety

Treat MCP tools as external capabilities with project-specific risk.

Minimum rules:

- Identify the tool and target environment before use.
- Do not expose secrets in packets or reports.
- Prefer read-only calls until write behavior is required.
- Record tool outputs as evidence when they affect implementation.
