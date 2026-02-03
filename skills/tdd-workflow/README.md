# TDD Workflow

This skill provides a comprehensive Test-Driven Development workflow and patterns.

## TDD Cycle

### 1. Red - Write a Failing Test
```javascript
// First, write a test that defines the desired behavior
describe('UserService', () => {
  it('should create a user with valid data', async () => {
    const userData = {
      name: 'John Doe',
      email: 'john@example.com',
      password: 'SecurePass123!'
    }
    
    const user = await userService.create(userData)
    
    expect(user).toBeDefined()
    expect(user.id).toBeDefined()
    expect(user.name).toBe(userData.name)
    expect(user.email).toBe(userData.email)
    expect(user.password).not.toBe(userData.password) // Should be hashed
    expect(user.createdAt).toBeInstanceOf(Date)
  })
})
```

### 2. Green - Make the Test Pass
```javascript
// Write the minimum code needed to pass the test
class UserService {
  async create(userData) {
    return {
      id: generateId(),
      name: userData.name,
      email: userData.email,
      password: await hash(userData.password),
      createdAt: new Date()
    }
  }
}
```

### 3. Refactor - Improve the Code
```javascript
// Refactor while keeping the test green
class UserService {
  constructor(userRepository, passwordHasher) {
    this.userRepository = userRepository
    this.passwordHasher = passwordHasher
  }
  
  async create(userData) {
    this.validateUserData(userData)
    
    const hashedPassword = await this.passwordHasher.hash(userData.password)
    const user = new User({
      ...userData,
      password: hashedPassword
    })
    
    return this.userRepository.save(user)
  }
  
  validateUserData(userData) {
    if (!userData.name || !userData.email || !userData.password) {
      throw new ValidationError('Missing required fields')
    }
    if (!isValidEmail(userData.email)) {
      throw new ValidationError('Invalid email format')
    }
    if (userData.password.length < 8) {
      throw new ValidationError('Password too short')
    }
  }
}
```

## TDD Patterns

### 1. Outside-In TDD
```javascript
// Start with an acceptance test
describe('User Registration', () => {
  it('should register a new user and send welcome email', async () => {
    // Test from the outside (API level)
    const response = await request(app)
      .post('/api/users/register')
      .send({
        name: 'John Doe',
        email: 'john@example.com',
        password: 'SecurePass123!'
      })
    
    expect(response.status).toBe(201)
    expect(response.body.user.email).toBe('john@example.com')
    
    // Verify side effects
    expect(emailService.sendWelcomeEmail).toHaveBeenCalledWith(
      'john@example.com',
      'John Doe'
    )
  })
})

// Then work inward with unit tests
describe('User Registration Service', () => {
  it('should create user and send email', async () => {
    const user = await registrationService.register(userData)
    expect(user).toBeDefined()
    expect(emailService.sendWelcomeEmail).toHaveBeenCalled()
  })
})
```

### 2. Mockist vs Classic TDD

#### Mockist TDD (Behavior verification)
```javascript
describe('OrderService', () => {
  it('should process payment and update inventory', async () => {
    const paymentGateway = mock(PaymentGateway)
    const inventory = mock(InventoryService)
    
    const orderService = new OrderService(paymentGateway, inventory)
    
    await orderService.processOrder(order)
    
    verify(paymentGateway.charge(order.total)).once()
    verify(inventory.reserveItems(order.items)).once()
  })
})
```

#### Classic TDD (State verification)
```javascript
describe('OrderService', () => {
  it('should process order successfully', async () => {
    const orderService = new OrderService(
      new FakePaymentGateway(),
      new FakeInventoryService()
    )
    
    const result = await orderService.processOrder(order)
    
    expect(result.status).toBe('completed')
    expect(result.paymentId).toBeDefined()
    expect(result.reservationId).toBeDefined()
  })
})
```

## Test Structure Patterns

### 1. AAA Pattern (Arrange, Act, Assert)
```javascript
it('should calculate discount for premium members', () => {
  // Arrange
  const customer = new Customer({ tier: 'premium' })
  const order = new Order({ total: 100 })
  const discountService = new DiscountService()
  
  // Act
  const discount = discountService.calculateDiscount(customer, order)
  
  // Assert
  expect(discount).toBe(20) // 20% discount for premium
})
```

### 2. Builder Pattern for Test Data
```javascript
class UserBuilder {
  constructor() {
    this.data = {
      name: 'John Doe',
      email: 'john@example.com',
      password: 'password123',
      tier: 'regular'
    }
  }
  
  withName(name) {
    this.data.name = name
    return this
  }
  
  withEmail(email) {
    this.data.email = email
    return this
  }
  
  asPremium() {
    this.data.tier = 'premium'
    return this
  }
  
  build() {
    return new User(this.data)
  }
}

// Usage
it('should handle premium user features', () => {
  const user = new UserBuilder()
    .withName('Jane Smith')
    .asPremium()
    .build()
  
  // Test with premium user
})
```

### 3. Test Data Factories
```javascript
// factories/userFactory.js
export const createUser = (overrides = {}) => {
  return {
    id: faker.datatype.uuid(),
    name: faker.name.findName(),
    email: faker.internet.email(),
    password: faker.internet.password(),
    tier: 'regular',
    createdAt: new Date(),
    ...overrides
  }
}

// Usage
it('should upgrade user tier', () => {
  const user = createUser({ tier: 'regular' })
  const upgradedUser = userService.upgradeTier(user.id, 'premium')
  
  expect(upgradedUser.tier).toBe('premium')
})
```

## Advanced TDD Techniques

### 1. Test-Driven API Design
```javascript
// First, define how you want to use the API
describe('FileProcessor', () => {
  it('should process CSV file and return structured data', async () => {
    const processor = new FileProcessor()
    
    const result = await processor.process('data.csv', {
      format: 'csv',
      delimiter: ',',
      headers: true
    })
    
    expect(result.data).toHaveLength(3)
    expect(result.data[0]).toEqual({
      name: 'John',
      age: 30,
      city: 'New York'
    })
    expect(result.metadata.rows).toBe(3)
    expect(result.metadata.columns).toBe(3)
  })
})

// Then implement to match the desired API
class FileProcessor {
  async process(filePath, options) {
    // Implementation that matches the test expectations
  }
}
```

### 2. Characterization Tests
```javascript
// When working with legacy code, first write tests to document current behavior
describe('Legacy Calculator', () => {
  it('should handle division by zero gracefully', () => {
    // Document what it actually does (even if wrong)
    expect(() => calculator.divide(10, 0)).not.toThrow()
    expect(calculator.divide(10, 0)).toBeNull()
  })
  
  it('should handle negative numbers unexpectedly', () => {
    // Document the bug before fixing
    expect(calculator.add(-5, 10)).toBe(15) // Works
    expect(calculator.multiply(-5, 10)).toBe(-50) // Works
    expect(calculator.sqrt(-9)).toBeNaN() // Returns NaN instead of throwing
  })
})
```

### 3. Property-Based Testing
```javascript
import fc from 'fast-check'

describe('String Utils', () => {
  it('should reverse string correctly', () => {
    fc.assert(fc.property(fc.string(), (str) => {
      const reversed = reverseString(str)
      return reverseString(reversed) === str
    }))
  })
  
  it('should split and rejoin correctly', () => {
    fc.assert(fc.property(fc.string(), fc.string(), (str, delimiter) => {
      if (delimiter.length === 0) return true // Skip empty delimiter
      
      const parts = str.split(delimiter)
      const rejoined = parts.join(delimiter)
      return rejoined === str
    }))
  })
})
```

## Testing Edge Cases

### 1. Boundary Testing
```javascript
describe('Array Utils', () => {
  describe('first', () => {
    it('should return first element', () => {
      expect(first([1, 2, 3])).toBe(1)
    })
    
    it('should handle empty array', () => {
      expect(first([])).toBeUndefined()
    })
    
    it('should handle single element array', () => {
      expect(first([42])).toBe(42)
    })
  })
})
```

### 2. Error Handling Tests
```javascript
describe('UserService', () => {
  it('should throw validation error for invalid email', async () => {
    await expect(
      userService.create({ email: 'invalid-email' })
    ).rejects.toThrow(ValidationError)
  })
  
  it('should handle database timeout gracefully', async () => {
    // Mock database timeout
    mockDatabase.setTimeout(true)
    
    await expect(
      userService.create(userData)
    ).rejects.toThrow('Database timeout')
  })
})
```

### 3. Async Testing Patterns
```javascript
describe('Async Operations', () => {
  it('should resolve with correct data', async () => {
    const promise = asyncOperation()
    
    await expect(promise).resolves.toEqual(expectedData)
  })
  
  it('should reject with error', async () => {
    const promise = failingAsyncOperation()
    
    await expect(promise).rejects.toThrow('Operation failed')
  })
  
  it('should handle multiple concurrent operations', async () => {
    const operations = Array.from({ length: 10 }, () => asyncOperation())
    
    const results = await Promise.all(operations)
    expect(results).toHaveLength(10)
  })
})
```

## Test Organization

### 1. Test Suite Structure
```
src/
  services/
    userService.js
    userService.test.js
  utils/
    stringUtils.js
    stringUtils.test.js
  __tests__/
    integration/
      userFlow.test.js
    fixtures/
      userData.js
    helpers/
      testHelpers.js
```

### 2. Test Categories
```javascript
// Unit tests - Fast, isolated
describe('UserService Unit Tests', () => {
  // Test individual methods
})

// Integration tests - Slower, test interactions
describe('UserService Integration Tests', () => {
  // Test with real database
})

// End-to-end tests - Slowest, test full flows
describe('User Registration E2E', () => {
  // Test through API
})
```

## Best Practices

1. **One Assertion Per Test**: Keep tests focused
2. **Descriptive Test Names**: Should document behavior
3. **Test Setup in beforeEach**: Keep tests DRY
4. **Avoid Test Interdependence**: Tests should run in any order
5. **Mock External Dependencies**: Keep tests fast and reliable
6. **Use Test Coverage Wisely**: Aim for meaningful coverage, not just percentage
7. **Refactor Tests**: Keep tests clean and maintainable

## Common Pitfalls

1. **Testing Implementation**: Test behavior, not how it's done
2. **Over-Mocking**: Mock too much and test nothing
3. **Fragile Tests**: Tests that break with implementation changes
4. **Ignoring Edge Cases**: Focus on happy path only
5. **Slow Tests**: Tests that take too long discourage running them
6. **Complex Test Setup**: Too much setup makes tests hard to understand

Remember: TDD is a design practice, not just a testing technique. The tests drive the design of your code, leading to better architecture and fewer bugs.
