# Coding Standards

This skill contains comprehensive coding standards and best practices for multiple programming languages.

## Language-Specific Standards

### JavaScript/TypeScript
- Use ESLint and Prettier for consistent formatting
- Prefer const over let, avoid var
- Use TypeScript for type safety
- Follow functional programming principles where appropriate
- Use meaningful variable and function names

### Python
- Follow PEP 8 style guide
- Use Black for code formatting
- Type hints are required for all public functions
- Docstrings follow Google style
- Maximum line length: 88 characters

### Go
- Use gofmt for formatting (no configuration needed)
- Exported names must have documentation comments
- Use golint for additional style checks
- Package names should be short, lowercase, single words
- Error messages should not start with capital letters

### Java
- Follow Google Java Style Guide
- Use Checkstyle for enforcement
- Maximum line length: 100 characters
- Use Javadoc for all public APIs
- Prefer immutable objects

## General Principles

1. **Readability First**: Code is read more often than written
2. **Consistency**: Follow established patterns within the codebase
3. **Simplicity**: Favor simple solutions over complex ones
4. **Documentation**: Document intent, not implementation
5. **Testing**: Write tests as you write code

## Code Review Checklist

- [ ] Code follows language-specific style guide
- [ ] Functions have single responsibilities
- [ ] Variable names are descriptive
- [ ] Complex logic has comments
- [ ] Error handling is appropriate
- [ ] No hardcoded values (use constants)
- [ ] Tests cover edge cases
