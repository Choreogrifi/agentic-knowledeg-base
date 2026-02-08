# Agentic Knowledge Base Playbook
## AI Engagement Assets

**Purpose:** Centralized, version-controlled repository for prompts, agent definitions, IDE skills, personas, and development documentation.

---

## Initial Setup

### 1. Create Vault Structure

```bash
# Create main vault directory
mkdir -p ~/agentic-knowledge-base
cd ~/agentic-knowledge-base

# Initialize git
git init
echo ".obsidian/workspace*" >> .gitignore
echo ".obsidian/cache" >> .gitignore
echo ".DS_Store" >> .gitignore

# Create core directory structure
mkdir -p {agents,skills,personas,prompts,templates,projects,reference}
```

### 2. Directory Organization

```
agentic-knowledge-base/
├── agents/                 # Multi-Agent SDLC Framework agents
│   ├── architect/
│   ├── engineer/
│   ├── tester/
│   ├── devops/
│   ├── security/
│   └── documenter/
├── skills/                 # IDE skills (Claude Code, Cursor, etc.)
│   ├── docx/
│   ├── pdf/
│   ├── pptx/
│   ├── xlsx/
│   ├── frontend-design/
│   └── _custom/           # Your custom skills
├── personas/              # Reusable AI personas
│   ├── consultancy/
│   ├── development/
│   └── personal/
├── prompts/               # Prompt templates & snippets
│   ├── chains/           # Multi-step prompt sequences
│   ├── system/           # System prompts
│   └── snippets/         # Reusable fragments
├── templates/             # Obsidian templates
└── reference/             # Documentation & research
    ├── apis/
    ├── patterns/
    └── workflows/
```

### 3. Obsidian Configuration

**Install Core Plugins:**
- Templates
- Daily notes (optional)
- Graph view
- Backlinks
- Command palette

**Install Community Plugins:**
- Templater (advanced templates)
- Dataview (query your vault)
- Git (auto-commit)
- Quick Switcher++
- Tag Wrangler

**Settings to configure:**
- Files & Links → Default location for new notes: "Same folder as current file"
- Files & Links → New link format: "Relative path to file"
- Editor → Strict line breaks: OFF
- Editor → Show frontmatter: ON

---

## Core Templates

### Agent Definition Template

```yaml
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

\`\`\`markdown
[Core system prompt that defines this agent's behavior]
\`\`\`

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
\`\`\`json
{
  "adapter": "claude",
  "config": {}
}
\`\`\`

### Gemini CLI
\`\`\`json
{
  "adapter": "gemini",
  "config": {}
}
\`\`\`

## Examples

### Example 1: [Scenario]
**Input:**
\`\`\`
[Input artifact]
\`\`\`

**Output:**
\`\`\`
[Expected output]
\`\`\`

## Related
- [[Related Agent 1]]
- [[Related Skill 1]]
- [[Related Workflow]]

## Changelog
- YYYY-MM-DD: Initial version
```

### Skill Definition Template

```yaml
---
type: skill
category: {{category}}
platforms:
  - claude-code
  - cursor
difficulty: beginner|intermediate|advanced
tags:
  - skill
  - {{category}}
created: {{date}}
modified: {{date}}
---

# {{Skill Name}}

## Overview
What this skill does and when to use it.

## Prerequisites
- Required knowledge
- Required tools
- Required setup

## Skill Prompt

\`\`\`markdown
[The actual skill prompt/instructions]
\`\`\`

## Usage Pattern

### Basic Usage
\`\`\`bash
# Command or invocation example
\`\`\`

### Advanced Usage
\`\`\`bash
# More complex examples
\`\`\`

## Integration Points
- How this skill integrates with agents
- How to reference from other prompts

## Examples

### Example 1: [Use Case]
**Context:**
[Setup/context]

**Execution:**
\`\`\`
[How to use the skill]
\`\`\`

**Result:**
[Expected outcome]

## Troubleshooting
Common issues and solutions.

## Related
- [[Related Skill 1]]
- [[Related Agent 1]]
- [[Related Project]]

## Changelog
- YYYY-MM-DD: Initial version
```

### Persona Template

```yaml
---
type: persona
context: {{context}}
tone: {{tone}}
tags:
  - persona
  - {{context}}
created: {{date}}
modified: {{date}}
---

# {{Persona Name}}

## Context
When and why to use this persona.

## Characteristics
- Trait 1
- Trait 2
- Communication style

## System Prompt Fragment

\`\`\`markdown
[Persona-defining prompt section that can be injected into other prompts]
\`\`\`

## Use Cases
1. Use case 1
2. Use case 2

## Example Conversations

### Example 1
**User:** [Input]
**Assistant (as persona):** [Response]

## Integration
How to combine this persona with agents/skills.

## Related
- [[Related Persona 1]]
- [[Related Project]]

## Changelog
- YYYY-MM-DD: Initial version
```

---

## Daily Workflows

### Morning: Vault Sync
```bash
cd ~/agentic-knowledge-base
git pull
# Review any changes from other machines
```

### During Development: Quick Capture

**Use Obsidian Quick Switcher (Cmd+O):**
1. Type note name
2. Create if doesn't exist
3. Use template if needed

**CLI Quick Capture:**
```bash
# Create new prompt snippet
echo "## {{title}}\n\n{{content}}" >> prompts/snippets/new-snippet.md

# Add to git
cd ~/agentic-knowledge-base
git add .
git commit -m "Add: new prompt snippet"
git push
```

### Evening: Vault Commit
```bash
cd ~/agentic-knowledge-base
git add .
git commit -m "Update: [description of changes]"
git push
```

---

## Integration Patterns

### 1. Reference from Claude Code

**In .claude/config.json:**
```json
{
  "skills_path": "~/agentic-knowledge-base/skills",
  "agents_path": "~/agentic-knowledge-base/agents"
}
```

**In prompt:**
```
Use the architect agent definition from ~/agentic-knowledge-base/agents/architect/definition.md
```

### 2. Reference from Cursor

**In .cursorrules:**
```markdown
# Agent Definitions
Load agent behaviors from ~/agentic-knowledge-base/agents/

# Skills
Reference skills from ~/agentic-knowledge-base/skills/
```

### 3. Reference from Gemini CLI

```bash
# Include agent context in prompt
gemini -f ~/agentic-knowledge-base/agents/engineer/definition.md "implement feature X"
```

### 4. Symlink to Project

```bash
# Link vault to active project
cd ~/projects/omni-chat
ln -s ~/agentic-knowledge-base ./kb

# Reference in project
cat kb/agents/architect/definition.md
```

---

## Maintenance Routines

### Weekly Review
- [ ] Review recent notes for proper tagging
- [ ] Check for orphaned notes (no backlinks)
- [ ] Update changelogs in modified agents/skills
- [ ] Clean up temporary notes
- [ ] Update index notes

### Monthly Audit
- [ ] Review agent effectiveness
- [ ] Update outdated skills
- [ ] Archive completed project notes
- [ ] Check graph view for structure issues
- [ ] Update this playbook

### Quarterly Optimization
- [ ] Refactor directory structure if needed
- [ ] Consolidate duplicate content
- [ ] Update templates based on usage
- [ ] Review and prune unused personas
- [ ] Major version bump for agents if significant changes

---

## Search & Discovery

### Quick Reference Commands

**Find all agents:**
```
path:agents/ type:agent
```

**Find skills by platform:**
```
path:skills/ platforms:claude-code
```

**Find recent updates:**
```
modified:7d
```

**Find by tag:**
```
tag:#prompt tag:#system
```

### Dataview Queries

**List all agents by role:**
```dataview
TABLE role, version, platforms
FROM "agents"
WHERE type = "agent"
SORT role ASC
```

**Recent skill updates:**
```dataview
TABLE modified, category, difficulty
FROM "skills"
WHERE type = "skill"
SORT modified DESC
LIMIT 10
```

---

## Backup Strategy

### Local Backups
```bash
# Time Machine handles this automatically
# Or manual backup:
rsync -av ~/agentic-knowledge-base ~/Backups/kb-backup-$(date +%Y%m%d)
```

### Remote Git Repository
```bash
# Add remote (GitHub private repo)
git remote add origin git@github.com/Choreogrifi/agentic-knowledeg-base
git push -u origin main

# Daily push via cron or Git plugin
```

### Cloud Sync (Optional)
- iCloud Drive sync for Obsidian vault
- But keep git as source of truth

---

## Advanced Patterns

### Prompt Chains

**Create linked prompts for complex workflows:**

`prompts/chains/feature-development.md`:
```yaml
---
type: chain
steps: 5
---

# Feature Development Chain

## Step 1: Requirements ➜ Architect Agent
[[agents/architect/definition]]
Input: Feature requirements
Output: Architecture decision record

## Step 2: Architecture ➜ Engineer Agent
[[agents/engineer/definition]]
Input: ADR from step 1
Output: Implementation plan

## Step 3: Implementation ➜ Tester Agent
[[agents/tester/definition]]
Input: Implementation from step 2
Output: Test plan

## Step 4: Testing ➜ DevOps Agent
[[agents/devops/definition]]
Input: Tests from step 3
Output: Deployment plan

## Step 5: Deployment ➜ Documenter Agent
[[agents/documenter/definition]]
Input: All previous artifacts
Output: Documentation
```

### Composite Skills

**Combine multiple skills:**

`skills/_custom/full-stack-deploy.md`:
```markdown
# Full Stack Deploy Skill

Composite skill combining:
- [[skills/frontend-design/definition]]
- [[skills/_custom/backend-api]]
- [[skills/_custom/devops-deploy]]

[Combined instructions...]
```

### Context Injection

**Reusable context blocks:**

`reference/contexts/choreogrifi-business.md`:
```markdown
# Choreogrifi Business Context

## Company Overview
[Standard business context to inject into consultancy-related prompts]

## Service Offerings
- AI Automation
- Enterprise Architecture
- Retail Media (planned)

## Tech Stack
[Standard tech stack description]
```

**Usage:**
```
{{reference/contexts/choreogrifi-business}}

Now, given this context, help me...
```

---

## Troubleshooting

### Obsidian won't open vault
- Check file permissions: `chmod -R u+rw ~/agentic-knowledge-base`
- Check for corrupt .obsidian folder
- Delete .obsidian/workspace and restart

### Git conflicts
```bash
# See what changed
git diff

# Accept your version
git checkout --ours <file>

# Accept incoming version
git checkout --theirs <file>

# Complete merge
git add .
git commit
```

### Performance issues
- Exclude large directories in .gitignore
- Disable heavy plugins temporarily
- Check vault size: `du -sh ~/agentic-knowledge-base`
- Consider splitting into multiple vaults if >1GB

### Broken links
- Use "Find orphaned files" in Command Palette
- Use "Check for broken links" plugin
- Search for old file paths: `[[old-path/`

---

## ADHD-Friendly Tips

### Reduce Friction
- Pin frequently used notes
- Create hotkeys for common templates
- Use quick switcher (Cmd+O) instead of browsing
- Set up daily note for brain dumps

### Visual Organization
- Use graph view to see connections
- Color-code folders in file explorer
- Use emoji in note titles for quick scanning
- Tag liberally for multiple access paths

### Prevent Overwhelm
- One inbox note for capture, process later
- Use templates to reduce decision fatigue
- Set up periodic reviews as calendar events
- Archive aggressively - out of sight, still searchable

### Leverage Hyperfocus
- When in flow, dump everything into vault
- Don't worry about organization during capture
- Refactor during lower-energy periods
- Use Dataview queries to find patterns later

---

## Quick Start Checklist

- [ ] Create vault directory structure
- [ ] Initialize git repository
- [ ] Set up .gitignore
- [ ] Configure Obsidian settings
- [ ] Install recommended plugins
- [ ] Create core templates
- [ ] Set up git remote
- [ ] Create first agent definition
- [ ] Create first skill
- [ ] Test CLI integration
- [ ] Add to morning routine

---

## Resources

- Obsidian Help: https://help.obsidian.md
- Markdown Reference: https://commonmark.org
- Git Basics: https://git-scm.com/book/en/v2
- Dataview Plugin: https://blacksmithgu.github.io/obsidian-dataview/

---

**Version:** 1.0  
**Last Updated:** 2026-02-08  
**Maintained By:** Leon @ Choreogrifi  
**Next Review:** 2026-03-08
