# /code-review - Code Review Command

## Description
Performs a comprehensive code review using the code reviewer agent.

## Usage
```
/code-review
/code-review --file=src/userService.js
/code-review --pr=123
/code-review --last-commit
```

## Implementation
The command delegates to the code reviewer agent which will:
1. Analyze code quality and maintainability
2. Check adherence to coding standards
3. Identify potential bugs and issues
4. Suggest improvements
5. Verify test coverage

## Review Areas

### 1. Code Quality
- Readability and clarity
- Function and class design
- Code organization
- Naming conventions
- Documentation

### 2. Best Practices
- Language-specific patterns
- Error handling
- Performance considerations
- Security practices
- Testing approach

### 3. Maintainability
- Code duplication
- Coupling and cohesion
- Extensibility
- Technical debt
- Dependencies

## Output Format

### Summary View
```markdown
# Code Review Report

## Overview
- **Files Reviewed**: 5
- **Issues Found**: 12
- **Score**: 85/100

## Critical Issues (2)
1. [Issue 1]
2. [Issue 2]

## Improvements (7)
1. [Improvement 1]
2. [Improvement 2]

## Positive Notes (3)
1. [Good practice 1]
2. [Good practice 2]
```

### Detailed Review
```markdown
# Code Review: userService.js

## ✅ Strengths
- Good error handling patterns
- Comprehensive test coverage
- Clear function documentation

## 🔍 Issues Found

### Critical
1. **Security**: Plain password comparison
   - Location: Line 45
   - Impact: Vulnerable to timing attacks
   - Fix: Use bcrypt.compare()

### Major
1. **Performance**: N+1 query in getUsers()
   - Location: Line 78
   - Impact: Database performance degradation
   - Fix: Use eager loading

### Minor
1. **Style**: Inconsistent naming
   - Location: Line 23
   - Suggestion: Use camelCase consistently

## Recommendations
1. Address critical security issue immediately
2. Refactor getUsers() to use joins
3. Consider adding input validation
```

## Examples

### Input
```
/code-review --file=src/paymentService.js
```

### Expected Output
```markdown
# Code Review: paymentService.js

## Summary
- **Overall Rating**: Good (78/100)
- **Security Issues**: 1 critical
- **Performance Issues**: 2 major
- **Style Issues**: 4 minor

## Critical Issue Found
**Payment Validation Bypass**
- Location: processPayment() at line 112
- Issue: Amount validation can be bypassed with negative values
- Risk: Financial loss
- Fix: Add absolute value check before processing

## Security Recommendations
1. Implement additional amount validation
2. Add rate limiting for payment endpoints
3. Log all payment attempts for audit

## Performance Improvements
1. Cache exchange rates for 5 minutes
2. Use database transactions for consistency
3. Implement async processing for notifications

## Code Quality
- Good separation of concerns
- Well-documented API
- Consider extracting payment providers to strategy pattern
