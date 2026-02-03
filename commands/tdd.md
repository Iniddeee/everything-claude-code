# /tdd - Test-Driven Development Command

## Description
Initiates a TDD workflow for implementing a feature using the TDD guide agent.

## Usage
```
/tdd "Create user service with CRUD operations"
/tdd "Implement payment processing with validation"
/tdd "Build email notification system"
```

## Implementation
The command delegates to the TDD guide agent which will:
1. Help write failing tests first
2. Guide implementation to pass tests
3. Assist with refactoring
4. Ensure test coverage
5. Follow TDD best practices

## Workflow Steps

### 1. Red Phase - Write Tests
The agent will help you:
- Define test cases based on requirements
- Write failing tests for each behavior
- Set up test structure and fixtures
- Choose appropriate testing tools

### 2. Green Phase - Implement
The agent will guide you to:
- Write minimal code to pass tests
- Avoid over-engineering
- Focus on one test at a time
- Run tests continuously

### 3. Refactor Phase - Improve
The agent will assist with:
- Code cleanup while maintaining green tests
- Extracting common patterns
- Improving design
- Adding missing tests

## Output Format

### Initial Response
```markdown
# TDD Session: [Feature Name]

Let's implement this using TDD methodology.

## Test Plan
1. [Test case 1]
2. [Test case 2]
3. [Test case 3]

## Starting with Test 1: [Description]
[Helps write the first failing test]
```

### Ongoing Guidance
```markdown
## Red Phase ✅
Test is failing as expected.

## Green Phase 🟢
Let's write the minimal code to pass this test.
[Guidance for implementation]

## Refactor Phase 🔧
Now that tests are green, let's improve the code.
[Refactoring suggestions]
```

## Examples

### Input
```
/tdd "Create a calculator service with add, subtract, multiply, divide"
```

### Expected Output
```markdown
# TDD Session: Calculator Service

Let's implement this calculator service using TDD methodology.

## Test Plan
1. Should add two positive numbers
2. Should handle negative numbers
3. Should multiply numbers
4. Should divide numbers
5. Should handle division by zero
6. Should handle decimal numbers

## Starting with Test 1: Add two positive numbers

Let's write our first test:

```javascript
describe('Calculator', () => {
  it('should add two positive numbers', () => {
    const calculator = new Calculator()
    const result = calculator.add(2, 3)
    expect(result).toBe(5)
  })
})
```

Run this test - it should fail because Calculator doesn't exist yet.
