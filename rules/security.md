# Security Rules

## Mandatory Security Practices

1. **Never hardcode secrets** - Use environment variables or secret managers
2. **Always validate input** - Server-side validation is required
3. **Use parameterized queries** - Prevent SQL injection
4. **Hash passwords** - Use bcrypt, scrypt, or argon2
5. **Encrypt sensitive data** - Both at rest and in transit
6. **Implement rate limiting** - Prevent abuse
7. **Use security headers** - CSP, HSTS, X-Frame-Options
8. **Log security events** - Track authentication failures, unauthorized access
9. **Keep dependencies updated** - Regular security patches
10. **Follow principle of least privilege** - Minimal necessary permissions
