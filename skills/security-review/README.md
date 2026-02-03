# Security Review

This skill provides comprehensive security review patterns and checklists for code and systems.

## Security Review Checklist

### 1. Authentication & Authorization
- [ ] Passwords are hashed with bcrypt/scrypt/argon2
- [ ] JWT tokens have appropriate expiration
- [ ] Refresh tokens are securely stored
- [ ] Multi-factor authentication is implemented
- [ ] Session management is secure (httpOnly cookies)
- [ ] Authorization checks are on every sensitive endpoint
- [ ] Principle of least privilege is applied

### 2. Input Validation & Sanitization
- [ ] All inputs are validated on server-side
- [ ] SQL injection prevention is in place
- [ ] XSS protection is implemented
- [ ] CSRF tokens are used for state-changing operations
- [ ] File upload restrictions are in place
- [ ] Input length limits are enforced
- [ ] Whitelist validation is used over blacklist

### 3. Data Protection
- [ ] Data in transit is encrypted (TLS 1.2+)
- [ ] Sensitive data is encrypted at rest
- [ ] PII is identified and protected
- [ ] Logs don't contain sensitive information
- [ ] Database credentials are secured
- [ ] API keys are not hardcoded
- [ ] Environment variables are used for secrets

### 4. API Security
- [ ] Rate limiting is implemented
- [ ] API versioning is used
- [ ] Proper HTTP status codes are returned
- [ ] Error messages don't leak information
- [ ] CORS is properly configured
- [ ] Security headers are set
- [ ] API documentation doesn't expose sensitive info

## Common Vulnerabilities & Fixes

### 1. SQL Injection
```javascript
// ❌ Vulnerable
const query = `SELECT * FROM users WHERE email = '${email}'`

// ✅ Fixed - Parameterized queries
const query = 'SELECT * FROM users WHERE email = ?'
db.query(query, [email])

// ✅ Better - ORM
const user = await User.findOne({ where: { email } })
```

### 2. Cross-Site Scripting (XSS)
```javascript
// ❌ Vulnerable
element.innerHTML = userInput

// ✅ Fixed - Text content
element.textContent = userInput

// ✅ Better - Sanitized HTML
element.innerHTML = DOMPurify.sanitize(userInput)
```

### 3. Insecure Direct Object Reference
```javascript
// ❌ Vulnerable
app.get('/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id)
  res.json(user)
})

// ✅ Fixed - Authorization check
app.get('/users/:id', async (req, res) => {
  if (req.user.id !== req.params.id && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' })
  }
  
  const user = await User.findById(req.params.id)
  res.json(user)
})
```

### 4. Hardcoded Secrets
```javascript
// ❌ Vulnerable
const apiKey = 'sk-1234567890abcdef'

// ✅ Fixed - Environment variables
const apiKey = process.env.API_KEY

// ✅ Better - Secret manager
const apiKey = await secretManager.getSecret('API_KEY')
```

## Security Headers

### Required Headers
```javascript
// Express.js example
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "'unsafe-inline'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", "data:", "https:"],
    },
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  }
}))

// Custom headers
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff')
  res.setHeader('X-Frame-Options', 'DENY')
  res.setHeader('Referrer-Policy', 'strict-origin-when-cross-origin')
  res.setHeader('Permissions-Policy', 'geolocation=()')
  next()
})
```

## Authentication Patterns

### 1. Secure Password Handling
```javascript
import bcrypt from 'bcrypt'

class AuthService {
  async hashPassword(password) {
    const saltRounds = 12
    return bcrypt.hash(password, saltRounds)
  }
  
  async verifyPassword(password, hash) {
    return bcrypt.compare(password, hash)
  }
  
  generateToken(user) {
    return jwt.sign(
      { 
        sub: user.id,
        email: user.email,
        role: user.role
      },
      process.env.JWT_SECRET,
      { 
        expiresIn: '15m',
        issuer: 'your-app',
        audience: 'your-app-users'
      }
    )
  }
  
  generateRefreshToken(user) {
    return jwt.sign(
      { sub: user.id },
      process.env.JWT_REFRESH_SECRET,
      { expiresIn: '7d' }
    )
  }
}
```

### 2. Session Security
```javascript
app.use(session({
  name: 'sessionId',
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    secure: process.env.NODE_ENV === 'production',
    httpOnly: true,
    maxAge: 1000 * 60 * 60 * 24, // 24 hours
    sameSite: 'strict'
  }
}))
```

## Data Validation Patterns

### 1. Input Validation
```javascript
import Joi from 'joi'

const userSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string()
    .min(8)
    .pattern(new RegExp('^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9])(?=.*[!@#\$%\^&\*])'))
    .required(),
  name: Joi.string().min(2).max(50).required(),
  age: Joi.number().integer().min(13).max(120)
})

const validateInput = (schema) => {
  return (req, res, next) => {
    const { error } = schema.validate(req.body)
    if (error) {
      return res.status(400).json({
        error: 'Validation failed',
        details: error.details.map(d => d.message)
      })
    }
    next()
  }
}
```

### 2. File Upload Security
```javascript
import multer from 'multer'
import path from 'path'

const fileFilter = (req, file, cb) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'application/pdf']
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true)
  } else {
    cb(new Error('Invalid file type'), false)
  }
}

const storage = multer.diskStorage({
  destination: 'uploads/',
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9)
    cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname))
  }
})

const upload = multer({
  storage,
  fileFilter,
  limits: {
    fileSize: 5 * 1024 * 1024 // 5MB limit
  }
})
```

## Security Testing

### 1. Security Unit Tests
```javascript
describe('Security Tests', () => {
  describe('Password Security', () => {
    it('should hash passwords with bcrypt', async () => {
      const password = 'password123'
      const hash = await authService.hashPassword(password)
      
      expect(hash).not.toBe(password)
      expect(hash).toMatch(/^\$2[aby]\$\d+\$/)
    })
    
    it('should not allow weak passwords', () => {
      const weakPasswords = ['123', 'password', 'qwerty']
      
      weakPasswords.forEach(password => {
        expect(() => validatePassword(password)).toThrow()
      })
    })
  })
  
  describe('Input Validation', () => {
    it('should sanitize HTML input', () => {
      const malicious = '<script>alert("xss")</script>'
      const sanitized = sanitizeHtml(malicious)
      
      expect(sanitized).not.toContain('<script>')
    })
    
    it('should prevent SQL injection', async () => {
      const maliciousEmail = "'; DROP TABLE users; --"
      
      await expect(
        userService.findByEmail(maliciousEmail)
      ).resolves.toBeNull()
    })
  })
})
```

### 2. Integration Security Tests
```javascript
describe('Security Integration Tests', () => {
  it('should require authentication for protected routes', async () => {
    const response = await request(app)
      .get('/api/profile')
      .expect(401)
    
    expect(response.body.error).toContain('Unauthorized')
  })
  
  it('should implement rate limiting', async () => {
    const requests = Array.from({ length: 101 }, () => 
      request(app).post('/api/login')
    )
    
    const responses = await Promise.all(requests)
    const rateLimitedResponses = responses.filter(r => r.status === 429)
    
    expect(rateLimitedResponses.length).toBeGreaterThan(0)
  })
})
```

## Security Monitoring

### 1. Security Logging
```javascript
const securityLogger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'security.log' })
  ]
})

const logSecurityEvent = (event, details) => {
  securityLogger.info({
    event,
    timestamp: new Date().toISOString(),
    ...details
  })
}

// Usage
app.use((req, res, next) => {
  if (req.path.includes('/admin') && !req.user.isAdmin) {
    logSecurityEvent('UNAUTHORIZED_ADMIN_ACCESS', {
      ip: req.ip,
      userAgent: req.get('User-Agent'),
      path: req.path
    })
  }
  next()
})
```

### 2. Intrusion Detection
```javascript
const detectSuspiciousActivity = (req, res, next) => {
  const ip = req.ip
  const userAgent = req.get('User-Agent')
  const path = req.path
  
  // Detect common attack patterns
  const attackPatterns = [
    /\.\./,  // Directory traversal
    /<script/i,  // XSS attempt
    /union.*select/i,  // SQL injection
    /cmd\.exe/i  // Command injection
  ]
  
  const isSuspicious = attackPatterns.some(pattern => 
    pattern.test(req.url) || 
    pattern.test(JSON.stringify(req.body))
  )
  
  if (isSuspicious) {
    logSecurityEvent('SUSPICIOUS_REQUEST', {
      ip,
      userAgent,
      path,
      body: req.body
    })
    
    // Optionally block the request
    return res.status(400).json({ error: 'Bad request' })
  }
  
  next()
}
```

## Security Review Process

### 1. Pre-Review Checklist
- [ ] Understand the application's data flow
- [ ] Identify all entry points (APIs, forms, uploads)
- [ ] List all sensitive data types
- [ ] Document authentication/authorization flows
- [ ] Review third-party dependencies

### 2. Review Steps
1. **Code Analysis**
   - Review authentication logic
   - Check input validation
   - Examine data handling
   - Review error messages

2. **Configuration Review**
   - Check security headers
   - Review CORS settings
   - Examine environment variables
   - Check logging configuration

3. **Dependency Review**
   - Check for known vulnerabilities
   - Review third-party services
   - Examine API integrations

### 3. Post-Review Actions
1. Document all findings
2. Prioritize by risk level
3. Create remediation plan
4. Verify fixes
5. Update security guidelines

## Best Practices

1. **Defense in Depth**: Multiple layers of security
2. **Principle of Least Privilege**: Minimum necessary access
3. **Secure by Default**: Secure configurations out of the box
4. **Regular Updates**: Keep dependencies updated
5. **Security Training**: Educate the team
6. **Incident Response**: Plan for security breaches
7. **Regular Audits**: Periodic security reviews

Remember: Security is an ongoing process, not a one-time checklist. Stay informed about new vulnerabilities and threats.
