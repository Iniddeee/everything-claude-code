# /plan - Implementation Planning Command

## Description
Creates a detailed implementation plan for a feature or task using the planner agent.

## Usage
```
/plan "Add user authentication system"
/plan "Implement payment processing"
/plan "Create real-time chat feature"
```

## Implementation
The command delegates to the planner agent which will:
1. Analyze the requirements
2. Break down into manageable tasks
3. Identify dependencies
4. Provide effort estimates
5. Highlight risks and mitigations

## Output Format
The planner agent will return a structured plan with:
- Overview and acceptance criteria
- Task breakdown by phases
- Dependencies and critical path
- Risk assessment
- Timeline estimates

## Examples

### Input
```
/plan "Add user authentication with email and social login"
```

### Expected Output
```markdown
# Implementation Plan: User Authentication System

## Overview
Implement a secure authentication system supporting email/password and OAuth2 social login.

## Acceptance Criteria
- Users can register with email/password
- Users can login with Google, GitHub, or Facebook
- Password reset functionality works
- Session management is secure
- 2FA is optional but available

## Task Breakdown

### Phase 1: Foundation (8h)
- [ ] Set up authentication service structure (2h)
- [ ] Configure JWT token generation (2h)
- [ ] Implement password hashing (2h)
- [ ] Create user model and migration (2h)

### Phase 2: Email Auth (12h)
- [ ] Build registration endpoint (3h)
- [ ] Build login endpoint (3h)
- [ ] Implement password reset (4h)
- [ ] Add email verification (2h)

### Phase 3: Social Auth (16h)
- [ ] Configure OAuth2 providers (4h)
- [ ] Implement Google OAuth (4h)
- [ ] Implement GitHub OAuth (4h)
- [ ] Implement Facebook OAuth (4h)

### Phase 4: Security & Testing (8h)
- [ ] Add rate limiting (2h)
- [ ] Implement 2FA (4h)
- [ ] Write comprehensive tests (2h)

## Dependencies
- Email service provider (SendGrid/SES)
- OAuth2 app registrations
- Database schema approval

## Risks & Mitigations
- **Risk**: OAuth provider rate limits
  - *Mitigation*: Implement caching and fallback flows
- **Risk**: Password security breach
  - *Mitigation*: Use bcrypt, implement 2FA, monitor breaches

## Total Estimate: 44 hours
```

## Notes
- The planner agent will ask clarifying questions if needed
- Plans are saved and can be referenced later
- Each task can be broken down further if requested
