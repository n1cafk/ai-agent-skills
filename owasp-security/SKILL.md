---
name: owasp-security
description: Implement secure coding practices following OWASP Top 10. Use when preventing security vulnerabilities, implementing authentication, securing APIs, or conducting security reviews. Triggers on OWASP, security, XSS, SQL injection, CSRF, authentication security, secure coding, vulnerability.
license: MIT
source: hoodini/ai-agents-skills
---

# OWASP Top 10 Security

Prevent common security vulnerabilities in web applications.

## OWASP Top 10 (2021)

| # | Vulnerability | Prevention |
|---|---------------|------------|
| A01 | Broken Access Control | Proper authorization checks |
| A02 | Cryptographic Failures | Strong encryption, secure storage |
| A03 | Injection | Input validation, parameterized queries |
| A04 | Insecure Design | Threat modeling, secure patterns |
| A05 | Security Misconfiguration | Hardened configs, no defaults |
| A06 | Vulnerable Components | Dependency scanning, updates |
| A07 | Auth Failures | MFA, secure session management |
| A08 | Data Integrity Failures | Input validation, signed updates |
| A09 | Logging Failures | Comprehensive audit logs |
| A10 | SSRF | URL validation, allowlists |

## A01: Broken Access Control

### Prevention Patterns
```typescript
// ❌ BAD: No authorization check
app.get('/api/users/:id', async (req, res) => {
  const user = await db.users.findById(req.params.id);
  res.json(user);
});

// ✅ GOOD: Verify ownership
app.get('/api/users/:id', authenticate, async (req, res) => {
  const userId = req.params.id;
  
  // Users can only access their own data
  if (req.user.id !== userId && req.user.role !== 'admin') {
    return res.status(403).json({ error: 'Forbidden' });
  }
  
  const user = await db.users.findById(userId);
  res.json(user);
});

// ✅ GOOD: Role-based access control (RBAC)
const requireRole = (...roles: string[]) => {
  return (req: Request, res: Response, next: NextFunction) => {
    if (!roles.includes(req.user?.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }
    next();
  };
};

app.delete('/api/posts/:id', authenticate, requireRole('admin', 'moderator'), deletePost);
```

### Insecure Direct Object Reference (IDOR)
```typescript
// ❌ BAD: Predictable IDs exposed
GET /api/invoices/1001
GET /api/invoices/1002 // Can enumerate others' invoices

// ✅ GOOD: Use UUIDs + ownership check
app.get('/api/invoices/:id', authenticate, async (req, res) => {
  const invoice = await db.invoices.findOne({
    id: req.params.id,
    userId: req.user.id, // Enforce ownership
  });
  
  if (!invoice) {
    return res.status(404).json({ error: 'Not found' });
  }
  
  res.json(invoice);
});
```

## A02: Cryptographic Failures

### Password Hashing
```typescript
import bcrypt from 'bcrypt';
import crypto from 'crypto';

// ✅ Hash passwords with bcrypt
const SALT_ROUNDS = 12;

async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}

// ✅ Secure token generation
function generateSecureToken(length = 32): string {
  return crypto.randomBytes(length).toString('hex');
}
```

### Secure Headers
```typescript
import helmet from 'helmet';

app.use(helmet());
app.use(helmet.hsts({ maxAge: 31536000, includeSubDomains: true }));
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'strict-dynamic'"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", 'data:', 'https:'],
    connectSrc: ["'self'"],
    fontSrc: ["'self'"],
    objectSrc: ["'none'"],
    frameAncestors: ["'none'"],
  },
}));
```

## A03: Injection

### SQL Injection Prevention
```typescript
// ❌ BAD: String concatenation
const query = `SELECT * FROM users WHERE email = '${email}'`;

// ✅ GOOD: Parameterized queries
// With Prisma
const user = await prisma.user.findUnique({ where: { email } });

// With raw SQL (parameterized)
const user = await db.query('SELECT * FROM users WHERE email = $1', [email]);

// With Knex
const user = await knex('users').where({ email }).first();
```

### NoSQL Injection Prevention
```typescript
// ❌ BAD: Direct user input in query
const user = await User.findOne({ username: req.body.username });
// Attack: { "username": { "$gt": "" } } returns first user

// ✅ GOOD: Validate input type
import { z } from 'zod';

const loginSchema = z.object({
  username: z.string().min(3).max(50),
  password: z.string().min(8),
});

app.post('/login', async (req, res) => {
  const { username, password } = loginSchema.parse(req.body);
  const user = await User.findOne({ username: String(username) });
  // ...
});
```

### Command Injection Prevention
```typescript
import { execFile } from 'child_process';

// ❌ BAD: Shell injection
exec(`convert ${userInput} output.png`); // userInput: "; rm -rf /"

// ✅ GOOD: Use execFile with array args
execFile('convert', [userInput, 'output.png'], (error, stdout) => {
  // Safe - arguments are not shell-interpreted
});
```

## A04: Insecure Design

### Rate Limiting
```typescript
import rateLimit from 'express-rate-limit';

// General rate limit
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  standardHeaders: true,
  legacyHeaders: false,
});

// Strict limit for auth endpoints
const authLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 5, // 5 failed attempts
  skipSuccessfulRequests: true,
});

app.use('/api/', limiter);
app.use('/api/auth/', authLimiter);
```

### Input Validation
```typescript
import { z } from 'zod';

const userSchema = z.object({
  email: z.string().email(),
  password: z.string()
    .min(8)
    .regex(/[A-Z]/, 'Must contain uppercase')
    .regex(/[a-z]/, 'Must contain lowercase')
    .regex(/[0-9]/, 'Must contain number')
    .regex(/[^A-Za-z0-9]/, 'Must contain special character'),
  age: z.number().int().min(13).max(120),
  role: z.enum(['user', 'admin']).default('user'),
});
```

## A05: Security Misconfiguration

### Environment Configuration
```typescript
// ✅ Never expose stack traces in production
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(err.stack); // Log for debugging
  
  res.status(500).json({
    error: process.env.NODE_ENV === 'production' 
      ? 'Internal server error' 
      : err.message,
  });
});

// ✅ Disable sensitive headers
app.disable('x-powered-by');

// ✅ Secure cookie configuration
app.use(session({
  secret: process.env.SESSION_SECRET!,
  cookie: {
    secure: process.env.NODE_ENV === 'production',
    httpOnly: true,
    sameSite: 'strict',
    maxAge: 24 * 60 * 60 * 1000, // 24 hours
  },
  resave: false,
  saveUninitialized: false,
}));
```

## A07: Authentication Failures

### Secure Session Management
```typescript
import jwt from 'jsonwebtoken';

// ✅ JWT with short expiry + refresh tokens
function generateTokens(userId: string) {
  const accessToken = jwt.sign(
    { userId },
    process.env.JWT_SECRET!,
    { expiresIn: '15m' } // Short-lived
  );
  
  const refreshToken = jwt.sign(
    { userId, type: 'refresh' },
    process.env.JWT_REFRESH_SECRET!,
    { expiresIn: '7d' }
  );
  
  return { accessToken, refreshToken };
}
```

## A08: XSS Prevention

```typescript
// ✅ React auto-escapes by default
const UserProfile = ({ user }) => (
  <div>{user.name}</div> // Safe - auto-escaped
);

// ✅ Sanitize HTML if needed
import DOMPurify from 'dompurify';

const sanitizedHtml = DOMPurify.sanitize(userHtml, {
  ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'a'],
  ALLOWED_ATTR: ['href'],
});
```

## A09: Logging & Monitoring

```typescript
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
});

// ✅ Log security events
function logSecurityEvent(event: string, details: object) {
  logger.warn({
    type: 'security',
    event,
    ...details,
    timestamp: new Date().toISOString(),
  });
}
```

## A10: SSRF Prevention

```typescript
import { URL } from 'url';

// ✅ Validate URLs against allowlist
const ALLOWED_HOSTS = ['api.example.com', 'cdn.example.com'];

function isAllowedUrl(urlString: string): boolean {
  try {
    const url = new URL(urlString);
    
    // Block private IPs
    const privatePatterns = [
      /^localhost$/i,
      /^127\./,
      /^10\./,
      /^172\.(1[6-9]|2[0-9]|3[01])\./,
      /^192\.168\./,
    ];
    
    if (privatePatterns.some(p => p.test(url.hostname))) {
      return false;
    }
    
    return ALLOWED_HOSTS.includes(url.hostname);
  } catch {
    return false;
  }
}
```

## Security Checklist

```markdown
## Pre-Deployment Checklist

### Authentication
- [ ] Passwords hashed with bcrypt (cost ≥ 12)
- [ ] JWT tokens have short expiry
- [ ] Session cookies are httpOnly, secure, sameSite
- [ ] Rate limiting on auth endpoints

### Authorization
- [ ] All endpoints have auth checks
- [ ] RBAC implemented correctly
- [ ] No IDOR vulnerabilities

### Input/Output
- [ ] All input validated with Zod/Joi
- [ ] SQL queries parameterized
- [ ] XSS prevented (CSP, escaping)
- [ ] File uploads validated and sandboxed

### Infrastructure
- [ ] HTTPS enforced
- [ ] Security headers configured
- [ ] Dependencies audited
- [ ] Secrets in environment variables
```

## Resources

- **OWASP Top 10**: https://owasp.org/Top10/
- **OWASP Cheat Sheets**: https://cheatsheetseries.owasp.org/
- **Node.js Security**: https://nodejs.org/en/docs/guides/security/
