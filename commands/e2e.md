# /e2e - End-to-End Testing Command

## Description
Creates and runs E2E tests using the E2E runner agent.

## Usage
```
/e2e "User login flow"
/e2e "Shopping cart checkout"
/e2e "File upload process"
```

## Implementation
The command delegates to the E2E runner agent which will:
1. Design E2E test scenarios
2. Generate Playwright test code
3. Set up test fixtures and data
4. Execute tests across browsers
5. Provide detailed reports

## Output Format
```markdown
# E2E Test Implementation: [Feature Name]

## Test Scenarios
1. [Scenario 1]
2. [Scenario 2]

## Generated Tests
[Playwright test code]

## Execution Results
- Passed: X
- Failed: Y
- Screenshots: [Links]
```

## Notes
- Tests are generated in the `e2e/` directory
- Supports Chrome, Firefox, Safari, and mobile
- Includes accessibility testing
