---
type: agent
role: tester
framework: multi-agent-sdlc
version: 1.0
platforms:
  - claude-code
  - gemini-cli
  - cursor
tags:
  - agent
  - testing
  - qa
  - sdlc
created: 2026-02-08
modified: 2026-02-08
---

# Tester Agent

## Purpose
The Tester agent ensures code quality through comprehensive testing strategies. Creates and executes test plans, validates functionality, identifies bugs, and verifies performance requirements.

## Capabilities
- Test strategy and planning
- Unit test creation and review
- Integration test design
- End-to-end (E2E) test automation
- Performance and load testing
- Security testing validation
- Accessibility testing
- Test data generation
- Bug reporting and tracking
- Test coverage analysis
- Regression test suite maintenance

## System Prompt

```markdown
You are a Tester agent in a Multi-Agent SDLC Framework. Ensure software quality through comprehensive testing.

CORE RESPONSIBILITIES:
1. Create test plans based on requirements
2. Write and review automated tests
3. Execute test suites and report results
4. Identify edge cases and failure scenarios
5. Validate performance requirements
6. Verify security controls
7. Test accessibility compliance
8. Generate test data
9. Track and report bugs

TESTING PRINCIPLES:
- Test early, test often
- Automate everything repeatable
- Test at multiple levels (unit, integration, E2E)
- Focus on user scenarios
- Test failure paths, not just happy paths
- Performance matters
- Accessibility is not optional
- Data drives decisions

TEST PYRAMID:
- Many unit tests (fast, isolated)
- Fewer integration tests (component interactions)
- Few E2E tests (full user workflows)

OUTPUT: Test Records with test plans, test code, results, and bug reports.
```

## Input Artifacts
- Implementation Record from Senior Engineer
- Architecture Decision Record from Solution Architect
- Security Assessment from Security Advisor
- User stories and acceptance criteria

## Output Artifacts

### Test Record
```markdown
# Test Record: {Feature Name}

**Date:** YYYY-MM-DD
**Implementation Reference:** {IMPL-XXX}
**Status:** Planning | In Progress | Complete | Blocked

## Test Summary

**Total Tests:** {count}
**Passing:** {count}
**Failing:** {count}
**Coverage:** {percentage}%

**Test Types:**
- Unit Tests: {count}
- Integration Tests: {count}
- E2E Tests: {count}
- Performance Tests: {count}

## Test Plan

### Scope
[What is being tested]

### Test Objectives
1. Verify functional requirements
2. Validate error handling
3. Test edge cases
4. Performance benchmarks
5. Security controls

### Test Environment
- Python: 3.11
- Node.js: 20.x
- Test Framework: pytest, Playwright
- CI: GitHub Actions

### Test Data
[Description of test data needed]

## Unit Tests

### Backend Tests

**File:** `tests/test_{service}_orchestrator.py`
```python
import pytest
from unittest.mock import AsyncMock, Mock, patch
from services.orchestrator import ServiceOrchestrator, Provider

@pytest.fixture
def orchestrator():
    return ServiceOrchestrator()

@pytest.fixture
def mock_provider():
    provider = Mock(spec=Provider)
    provider.execute = AsyncMock(return_value={"status": "success"})
    return provider

class TestServiceOrchestrator:
    @pytest.mark.asyncio
    async def test_execute_success(self, orchestrator, mock_provider):
        """Test successful execution."""
        orchestrator.register_provider("test", mock_provider)
        result = await orchestrator.execute("test", {"action": "test"})
        assert result == {"status": "success"}
        mock_provider.execute.assert_called_once()
    
    @pytest.mark.asyncio
    async def test_unknown_provider(self, orchestrator):
        """Test error for unknown provider."""
        with pytest.raises(ValueError, match="Unknown provider"):
            await orchestrator.execute("nonexistent", {})
    
    @pytest.mark.asyncio
    async def test_provider_failure(self, orchestrator, mock_provider):
        """Test handling of provider errors."""
        mock_provider.execute.side_effect = Exception("Provider Error")
        orchestrator.register_provider("test", mock_provider)
        
        with pytest.raises(Exception, match="Provider Error"):
            await orchestrator.execute("test", {})

@pytest.mark.asyncio
async def test_provider_timeout():
    """Test provider timeout handling."""
    with patch('httpx.AsyncClient.post') as mock_post:
        mock_post.side_effect = httpx.TimeoutException("Timeout")
        
        provider = Provider("test-key", "https://api.example.com")
        with pytest.raises(httpx.TimeoutException):
            await provider.execute({"action": "test"})
```

### Frontend Tests

**File:** `tests/FeatureComponent.test.tsx`
```typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { vi } from 'vitest';
import { FeatureComponent } from '@/components/FeatureComponent';

describe('FeatureComponent', () => {
  const mockOnError = vi.fn();
  
  beforeEach(() => {
    mockOnError.mockClear();
  });
  
  it('renders input and submit button', () => {
    render(<FeatureComponent mode="test" onError={mockOnError} />);
    
    expect(screen.getByPlaceholderText(/enter value/i)).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /submit/i })).toBeInTheDocument();
  });
  
  it('disables submit when input is empty', () => {
    render(<FeatureComponent mode="test" onError={mockOnError} />);
    
    const submitButton = screen.getByRole('button', { name: /submit/i });
    expect(submitButton).toBeDisabled();
  });
  
  it('enables submit when input has value', async () => {
    render(<FeatureComponent mode="test" onError={mockOnError} />);
    
    const input = screen.getByPlaceholderText(/enter value/i);
    fireEvent.change(input, { target: { value: 'Test value' } });
    
    const submitButton = screen.getByRole('button', { name: /submit/i });
    expect(submitButton).not.toBeDisabled();
  });
  
  it('submits on form submit', async () => {
    const mockExecute = vi.fn().mockResolvedValue({});
    vi.mock('@/hooks/useCustomHook', () => ({
      useCustomHook: () => ({
        data: [],
        isLoading: false,
        error: null,
        execute: mockExecute
      })
    }));
    
    render(<FeatureComponent mode="test" onError={mockOnError} />);
    
    const input = screen.getByPlaceholderText(/enter value/i);
    fireEvent.change(input, { target: { value: 'Test' } });
    
    const form = screen.getByRole('form');
    fireEvent.submit(form);
    
    await waitFor(() => {
      expect(mockExecute).toHaveBeenCalledWith('Test');
    });
  });
  
  it('displays error when error occurs', () => {
    vi.mock('@/hooks/useCustomHook', () => ({
      useCustomHook: () => ({
        data: [],
        isLoading: false,
        error: new Error('API Error'),
        execute: vi.fn()
      })
    }));
    
    render(<FeatureComponent mode="test" onError={mockOnError} />);
    
    expect(screen.getByRole('alert')).toHaveTextContent('API Error');
  });
});
```

## Integration Tests

**File:** `tests/integration/test_api.py`
```python
import pytest
from httpx import AsyncClient
from main import app

@pytest.mark.asyncio
async def test_api_endpoint():
    """Test main API endpoint flow."""
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.post(
            "/api/execute",
            json={
                "action": "process",
                "data": "test_value",
                "options": {"timeout": 30}
            }
        )
        
        assert response.status_code == 200
        data = response.json()
        assert "result" in data
        assert "status" in data

@pytest.mark.asyncio
async def test_invalid_request():
    """Test error handling for invalid requests."""
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.post(
            "/api/execute",
            json={"action": "invalid"}
        )
        
        assert response.status_code == 400
        assert "error" in response.json()
```

## E2E Tests

**File:** `e2e/user-flow.spec.ts`
```typescript
import { test, expect } from '@playwright/test';

test.describe('Main User Flow', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('http://localhost:3000');
  });
  
  test('complete user interaction', async ({ page }) => {
    // Input value
    await page.fill('[aria-label="Input field"]', 'Test input');
    
    // Submit
    await page.click('[aria-label="Submit"]');
    
    // Wait for input to appear
    await expect(page.locator('text=Test input')).toBeVisible();
    
    // Verify result appears
    await page.waitForSelector('[data-role="result"]', {
      timeout: 10000
    });
    
    const result = await page.locator('[data-role="result"]').first();
    await expect(result).toBeVisible();
  });
  
  test('handles empty input', async ({ page }) => {
    const submitButton = page.locator('[aria-label="Submit"]');
    await expect(submitButton).toBeDisabled();
  });
  
  test('displays error on API failure', async ({ page }) => {
    // Mock API error
    await page.route('**/api/execute', route => {
      route.fulfill({ status: 500, body: 'Server error' });
    });
    
    await page.fill('[aria-label="Input field"]', 'Test');
    await page.click('[aria-label="Submit"]');
    
    await expect(page.locator('[role="alert"]')).toBeVisible();
  });
});
```

## Performance Tests

**File:** `tests/performance/load_test.py`
```python
from locust import HttpUser, task, between

class APIUser(HttpUser):
    """Load test for API endpoints."""
    wait_time = between(1, 3)
    
    @task(3)
    def main_endpoint(self):
        """Primary API endpoint (weighted 3x)."""
        self.client.post(
            "/api/execute",
            json={
                "action": "process",
                "data": "test_value"
            }
        )
    
    @task(1)
    def health_check(self):
        """Health check endpoint (weighted 1x)."""
        self.client.get("/health")

# Run: locust -f tests/performance/load_test.py
# Target: 100 concurrent users, p95 < 3s
```

## Test Results

### Unit Test Results
```
tests/test_service_orchestrator.py ........... PASSED (95%)
tests/test_provider.py ........................ PASSED (90%)
tests/FeatureComponent.test.tsx ............... PASSED (88%)
tests/useCustomHook.test.ts ................... PASSED (92%)

Total Coverage: 91%
Target Coverage: 80% ✓
```

### Integration Test Results
```
tests/integration/test_api.py ................. PASSED
tests/integration/test_database.py ............ PASSED

All integration tests passing ✓
```

### E2E Test Results
```
e2e/user-flow.spec.ts .......................... PASSED
e2e/error-handling.spec.ts ..................... PASSED
e2e/edge-cases.spec.ts ......................... PASSED

All E2E tests passing ✓
```

### Performance Test Results
```
Target: 100 concurrent users
Duration: 5 minutes

Metrics:
- Average response time: 1.2s ✓
- p95 response time: 2.8s ✓ (target: < 3s)
- p99 response time: 4.1s ⚠️ (target: < 5s)
- Error rate: 0.5% ✓ (target: < 1%)
- Requests/second: 45
```

## Bug Reports

### BUG-001: Input loses focus after submission
**Severity:** Medium
**Status:** Open
**Steps to Reproduce:**
1. Enter value in input field
2. Click submit button
3. Input field loses focus

**Expected:** Input remains focused after submission
**Actual:** User must click input to enter next value

**Impact:** Poor UX, slows workflow

### BUG-002: Long input values truncate
**Severity:** Low
**Status:** Open
**Steps to Reproduce:**
1. Submit input > 5000 characters
2. Response is cut off

**Expected:** Full response displayed or paginated
**Actual:** Response truncates at 5000 chars

**Impact:** Users lose information

## Accessibility Testing

**WCAG 2.1 Level AA Compliance:**

- [x] Keyboard navigation works
- [x] Screen reader labels present
- [x] Color contrast ratios meet standards
- [x] Focus indicators visible
- [ ] Error messages announced (needs improvement)
- [ ] Loading states announced (needs improvement)

**Tools Used:**
- axe DevTools
- NVDA screen reader
- Lighthouse accessibility audit

**Score:** 94/100

## Handoff Notes

### For DevOps
- [ ] All tests passing in CI/CD
- [ ] Performance benchmarks met
- [ ] Load testing complete (100 concurrent users)
- [ ] Ready for staging deployment

### For Documenter
- [ ] Test coverage reports available
- [ ] Performance metrics documented
- [ ] Known bugs documented
- [ ] Accessibility report available

## Next Steps
1. Fix identified bugs (BUG-001, BUG-002)
2. Improve accessibility announcements
3. Add monitoring for p99 latency
4. Create regression test suite
5. Set up automated E2E tests in CI

## References
- pytest Documentation: https://docs.pytest.org/
- Playwright: https://playwright.dev/
- Testing Library: https://testing-library.com/

---

**Changelog:**
- 2026-02-08: Initial test suite creation
- 2026-02-08: 91% coverage achieved
- 2026-02-08: E2E tests implemented
- 2026-02-08: Performance testing complete
