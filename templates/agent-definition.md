---
type: agent
role: {{role}}
framework: multi-agent-sdlc
version: 1.0
platforms:
  - claude-code
  - gemini-cli
  - cursor
tags:
  - agent
  - {{role}}
created: {{date}}
modified: {{date}}
---

# {{role}} Agent

## Purpose
Brief description of this agent's role in the SDLC framework.

## Capabilities
- Capability 1
- Capability 2
- Capability 3

## System Prompt

```markdown
[Core system prompt that defines this agent's behavior]
```

## Input Artifacts
- Artifact type 1 (from Agent X)
- Artifact type 2 (from Agent Y)

## Output Artifacts
- Artifact type 1 (markdown format)
- Artifact type 2 (with approval gates)

## Coordination Protocol
- How this agent communicates with other agents
- Handoff procedures
- Approval requirements

## Platform-Specific Adaptations

### Claude Code
```json
{
  "adapter": "claude",
  "config": {}
}
```

### Gemini CLI
```json
{
  "adapter": "gemini",
  "config": {}
}
```

## Examples

### Example 1: [Scenario]
**Input:**
```
[Input artifact]
```

**Output:**
```
[Expected output]
```

## Related
- [[Related Agent 1]]
- [[Related Skill 1]]
- [[Related Workflow]]

## Changelog
- YYYY-MM-DD: Initial version
