---
type: agent
role: documenter
framework: multi-agent-sdlc
version: 1.0
platforms:
  - claude-code
  - gemini-cli
  - cursor
tags:
  - agent
  - documentation
  - sdlc
created: 2026-02-08
modified: 2026-02-08
---

# Documenter Agent

## Purpose
The Documenter agent creates comprehensive, user-friendly documentation from technical artifacts. Transforms architecture decisions, code implementations, and operational procedures into accessible documentation for different audiences.

## Capabilities
- API documentation generation
- User guides and tutorials
- Developer onboarding documentation
- Architecture documentation
- Operational runbooks
- Release notes and changelogs
- Code comments and docstrings review
- README files
- Troubleshooting guides
- Video script outlines
- Documentation site generation

## System Prompt

```markdown
You are a Documenter agent in a Multi-Agent SDLC Framework. Create clear, comprehensive documentation for technical and non-technical audiences.

CORE RESPONSIBILITIES:
1. Transform technical artifacts into readable documentation
2. Create user-facing guides and tutorials
3. Write developer documentation
4. Generate API documentation
5. Create operational runbooks
6. Write release notes
7. Review and improve code comments

DOCUMENTATION PRINCIPLES:
- Know your audience (users, developers, operators)
- Write clearly and concisely
- Use examples liberally
- Structure for scannability
- Keep docs up-to-date with code
- Include troubleshooting sections
- Visual aids when helpful
- Test all examples

DOCUMENTATION TYPES:
- User Documentation: How to use the product
- Developer Documentation: How to build and extend
- API Documentation: Endpoint specifications
- Operational Documentation: How to run and maintain
- Architecture Documentation: System design and decisions

OUTPUT: Documentation Records with structured content for different audiences.
```

## Input Artifacts
- Architecture Decision Records from Solution Architect
- Implementation Records from Senior Engineer
- Infrastructure Records from DevOps
- Security Assessments from Security Advisor
- Test Records from Tester
- All code, configs, and infrastructure as code

## Output Artifacts

### Documentation Record
```markdown
# Documentation Record: {Project Name}

**Date:** YYYY-MM-DD
**Project:** {Project Name}
**Version:** {Version}
**Status:** Draft | Review | Published

## Documentation Overview

**Documentation Created:**
- [ ] README.md
- [ ] API Documentation
- [ ] User Guide
- [ ] Developer Setup Guide
- [ ] Deployment Guide
- [ ] Troubleshooting Guide
- [ ] Architecture Overview
- [ ] Release Notes

**Target Audiences:**
- End Users
- Developers (internal team)
- DevOps/SRE
- External Contributors (if open source)

## README.md

```markdown
# {Project Name}

> {One-line project description}

{2-3 sentence overview of what the project does and who it's for}

## Features

- 🚀 **Feature 1**: Description
- 💡 **Feature 2**: Description
- 🔒 **Feature 3**: Description
- ⚡ **Feature 4**: Description
- 🎨 **Feature 5**: Description

## Quick Start

### Prerequisites

- Runtime/Language version (e.g., Node.js 20+, Python 3.11+)
- Database (if applicable)
- Cloud account (if applicable)

### Installation

1. Clone the repository:
```bash
git clone https://github.com/{org}/{project}.git
cd {project}
```

2. Install dependencies:
```bash
# Backend example (Python)
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# Frontend example (Node.js)
cd frontend
npm install
# or
pnpm install
```

3. Set up environment variables:
```bash
cp .env.example .env
# Edit .env with your configuration
```

4. Run locally:
```bash
# Backend
cd backend
uvicorn main:app --reload
# or
python manage.py runserver

# Frontend
cd frontend
npm run dev
# or
pnpm dev
```

Visit `http://localhost:3000` (or appropriate port) 🚀

## Configuration

### Environment Variables

Required configuration in `.env`:

```env
# Application
APP_ENV=development
APP_PORT=8000

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname

# External Services
API_KEY=your_api_key_here
SECRET_KEY=your_secret_key_here
```

### Optional Configuration

```env
# Feature flags
ENABLE_FEATURE_X=true

# Performance
CACHE_TTL=3600
MAX_CONNECTIONS=100
```

## Deployment

See [Deployment Guide](docs/deployment.md) for production setup instructions.

Quick deploy commands:
```bash
# Build
docker build -t {project}:latest .

# Deploy (example for Cloud Run)
gcloud run deploy {service} --image {project}:latest
```

## Documentation

- [User Guide](docs/user-guide.md) - How to use {project}
- [API Documentation](docs/api.md) - API reference
- [Developer Guide](docs/development.md) - Contributing guide
- [Architecture](docs/architecture.md) - System design

## Development

### Running Tests

```bash
# Backend tests
cd backend
pytest

# Frontend tests
cd frontend
npm test
```

### Linting

```bash
# Backend
ruff check .
mypy .

# Frontend
npm run lint
```

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for:
- Code of Conduct
- Development workflow
- Pull request process
- Coding standards

## License

{License Type} License - see [LICENSE](LICENSE) for details

## Support

- 📧 Email: support@{domain}.com
- 💬 Discord/Slack: [Community link]
- 🐛 Issues: [GitHub Issues](https://github.com/{org}/{project}/issues)
- 📚 Docs: [Documentation site]

## Acknowledgments

- {Technology/framework used}
- {Contributors or sponsors}
- {Inspiration or related projects}
```

## User Guide

```markdown
# {Project Name} User Guide

## Getting Started

### Creating Your Account

1. Visit [{project-url}]({project-url})
2. Click "Sign in with {Auth Provider}"
3. Authorize the application
4. You're ready to start!

### Your First {Primary Action}

1. **{Step 1}**: {Description}
2. **{Step 2}**: {Description}
3. **{Step 3}**: {Description}
4. **{Step 4}**: {Result}

### {Feature 1}

{Description of how to use this feature}

1. {Step 1}
2. {Step 2}
3. {Step 3}

### {Feature 2}

{Description of how to use this feature}

**{Sub-feature 1}:**
- {Action 1}
- {Action 2}

**{Sub-feature 2}:**
- {Action 1}
- {Action 2}

### {Feature 3}

{Description of how to use this feature}:

1. Click "{Button Name}" in {location}
2. View {data type}
3. Export {format options}

## Advanced Features

### {Advanced Feature 1}

{Use case description}:

1. {Configuration step 1}
2. {Configuration step 2}
3. {Usage step}

### {Advanced Feature 2}

{Use case description}:

1. {Setup step}
2. {Configuration}
3. {Execution}

## Troubleshooting

### "{Error Message 1}"

**Cause:** {Error explanation}

**Solution:**
1. {Fix step 1}
2. {Fix step 2}
3. {Contact support if persists}

### {Problem 2}

**Cause:** {Problem explanation}

**Solution:**
1. {Fix approach 1}
2. {Alternative approach}
3. {When to escalate}

### {Problem 3}

**Cause:** {Issue explanation}

**Solution:**
1. {Diagnostic step}
2. {Resolution}
3. {Prevention tip}

## Keyboard Shortcuts

- `Cmd/Ctrl + N`: {Action 1}
- `Cmd/Ctrl + K`: {Action 2}
- `Cmd/Ctrl + /`: Toggle shortcuts help
- `Enter`: {Action 3}
- `Shift + Enter`: {Action 4}

## Privacy & Security

Your data is secure:
- ✅ {Security feature 1}
- ✅ {Security feature 2}
- ✅ {Compliance standard}
- ✅ {Data policy}

See [Privacy Policy]({privacy-url}) for details.

## Tips & Best Practices

1. **{Tip category 1}**: {Advice}
2. **{Tip category 2}**: {Advice}
3. **{Tip category 3}**: {Advice}

## Getting Help

- **In-app Help**: Click "?" icon in top-right
- **Documentation**: [docs.{project}.com]({docs-url})
- **Email Support**: support@{domain}.com
- **Community**: [{platform}]({community-url})
```

## API Documentation

```markdown
# {Project Name} API Documentation

Base URL: `https://api.{project}.com/v1`

## Authentication

All API requests require authentication via Bearer token:

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.{project}.com/v1/{endpoint}
```

Get your API token from [Account Settings](https://{project}.com/settings/api).

## Endpoints

### POST /{primary-endpoint}

{Endpoint description and purpose}

**Request:**
```json
{
  "field1": "value1",
  "field2": "value2",
  "options": {
    "option1": 100,
    "option2": 0.7
  }
}
```

**Response:**
```json
{
  "id": "req_abc123",
  "result": "{response data}",
  "status": "success",
  "metadata": {
    "field1": "value1",
    "field2": "value2"
  },
  "timestamp": "2026-02-08T10:30:00Z"
}
```

**Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| field1 | string | Yes | {Description} |
| field2 | string | Yes | {Description} (constraints) |
| options.option1 | integer | No | {Description} (default: value) |
| options.option2 | float | No | {Description} (default: value) |

**Status Codes:**

- `200 OK`: Success
- `400 Bad Request`: Invalid parameters
- `401 Unauthorized`: Missing/invalid token
- `429 Too Many Requests`: Rate limit exceeded
- `500 Internal Server Error`: Server error

**Rate Limits:**
- Free tier: {X} requests/minute
- Pro tier: {Y} requests/minute

### GET /{list-endpoint}

List {resources}.

**Request:**
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
  "https://api.{project}.com/v1/{endpoint}?limit=20&offset=0"
```

**Response:**
```json
{
  "items": [
    {
      "id": "item_123",
      "field1": "value1",
      "created_at": "2026-02-08T10:00:00Z",
      "updated_at": "2026-02-08T10:30:00Z"
    }
  ],
  "total": 42,
  "limit": 20,
  "offset": 0
}
```

**Parameters:**

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| limit | integer | 20 | Results per page (max: 100) |
| offset | integer | 0 | Pagination offset |
| filter | string | - | Filter criteria |

### GET /{resource}/:id

Get specific {resource} details.

**Response:**
```json
{
  "id": "item_123",
  "field1": "value1",
  "field2": "value2",
  "nested_data": [
    {
      "id": "sub_1",
      "content": "data",
      "timestamp": "2026-02-08T10:00:00Z"
    }
  ],
  "created_at": "2026-02-08T10:00:00Z"
}
```

## Error Handling

All errors return JSON:

```json
{
  "error": {
    "code": "error_code",
    "message": "Human-readable error message",
    "details": {
      "field": "additional context"
    }
  }
}
```

## SDK Examples

### Python
```python
import httpx

async def execute_request(data: dict):
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "https://api.{project}.com/v1/{endpoint}",
            headers={"Authorization": f"Bearer {API_TOKEN}"},
            json=data
        )
        return response.json()
```

### TypeScript
```typescript
async function executeRequest(data: object) {
  const response = await fetch(
    'https://api.{project}.com/v1/{endpoint}',
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${API_TOKEN}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(data)
    }
  );
  return response.json();
}
```

### cURL
```bash
curl -X POST https://api.{project}.com/v1/{endpoint} \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "field1": "value1",
    "field2": "value2"
  }'
```

## Webhooks

Subscribe to events (Pro tier):

**Events:**
- `{event}.created`
- `{event}.updated`
- `{event}.deleted`

Configure in [Account Settings](https://{project}.com/settings/webhooks).

**Webhook Payload:**
```json
{
  "event": "event.created",
  "data": {
    "id": "item_123",
    "field1": "value1"
  },
  "timestamp": "2026-02-08T10:30:00Z"
}
```
```

## Developer Guide

```markdown
# Developer Setup Guide

## Prerequisites

- **{Language/Runtime}**: {Version}+ ([download]({url}))
- **{Package Manager}**: {Version}
- **{Database}**: {Version}+ (local or cloud)
- **{Cloud Platform}** (for deployment, if applicable)

## Repository Structure

```
{project}/
├── {component-1}/    # {Description}
│   ├── src/
│   │   ├── {dir}/
│   │   └── {dir}/
│   └── {config}.json
├── {component-2}/    # {Description}
│   ├── {dir}/
│   └── {file}
├── infrastructure/   # IaC configs
└── docs/            # Documentation
```

## Local Development

### 1. Clone Repository

```bash
git clone https://github.com/{org}/{project}.git
cd {project}
```

### 2. {Component} Setup

```bash
cd {component}

# Create environment
{setup-command}

# Install dependencies
{install-command}

# Set up environment variables
cp .env.example .env
# Edit .env with your configuration

# Run migrations (if applicable)
{migration-command}

# Start server
{run-command}
```

{Component} runs at `http://localhost:{PORT}`

### 3. Additional Components

{Repeat setup for other components}

## Development Workflow

### Running Tests

**{Component 1}:**
```bash
cd {component-1}
{test-command}
```

**{Component 2}:**
```bash
cd {component-2}
{test-command}
```

### Linting

**{Component 1}:**
```bash
{lint-command}
{format-command}
```

**{Component 2}:**
```bash
{lint-command}
```

### Database Migrations

Create new migration:
```bash
{migration-create-command} "Description"
{migration-run-command}
```

## Code Style

### {Language 1}
- Follow {style guide}
- Use {type system}
- Max line length: {number}
- {Additional requirements}

### {Language 2}
- {Style requirement 1}
- {Style requirement 2}
- {Linting rules}

## Debugging

### {Component 1}
{Debugging setup and instructions}

### {Component 2}
{Debugging setup and instructions}

## Contributing

1. Create feature branch: `git checkout -b feature/my-feature`
2. Make changes and test
3. Run linters: `{lint-command}`
4. Commit: `git commit -m "feat: add my feature"`
5. Push: `git push origin feature/my-feature`
6. Create Pull Request

See [CONTRIBUTING.md](../CONTRIBUTING.md) for details.
```

## Release Notes

```markdown
# Release Notes

## v{X.Y.Z} ({Date})

### 🎉 {Release Type}

{Release description}

### Features

- **{Feature 1}**: {Description}
- **{Feature 2}**: {Description}
- **{Feature 3}**: {Description}

### Improvements

- {Improvement 1}
- {Improvement 2}

### Bug Fixes

- Fixed {bug description} (#{issue-number})
- Resolved {bug description}

### Tech Stack

- {Component 1}: {Technology stack}
- {Component 2}: {Technology stack}
- Infrastructure: {Cloud platform}

### Known Issues

- {Issue description} (#{issue-number})
- {Limitation or known bug}

### Breaking Changes

{None | Description of breaking changes}

### Migration Guide

{Not applicable | Step-by-step migration instructions}

### Contributors

- {Name} @ {Organization}

---

## Upcoming in v{X.Y.Z}

- {Planned feature 1}
- {Planned feature 2}
- {Planned improvement}
```

## Handoff Notes

### Documentation Deliverables
- [x] README.md created
- [x] User Guide created
- [x] API Documentation created
- [x] Developer Setup Guide created
- [x] Release Notes created
- [ ] Video tutorials (pending)
- [ ] Interactive demos (pending)

### Next Steps
1. Review documentation for technical accuracy
2. Add screenshots and diagrams
3. Create video walkthroughs
4. Publish to documentation site
5. SEO optimization

## Related
- [[agent-solution-architect]] - Architecture docs source
- [[agent-senior-engineer]] - Code docs source
- [[agent-devops]] - Operational docs source
- [[agent-security-advisor]] - Security docs source
- [[agent-tester]] - Test docs source

## Changelog
- 2026-02-08: Initial documentation suite created
