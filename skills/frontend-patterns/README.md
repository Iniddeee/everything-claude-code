# Frontend Patterns

This skill contains modern frontend development patterns and best practices.

## Component Architecture

### 1. Atomic Design
```
atoms/     (buttons, inputs, labels)
molecules/ (search box, user card)
organisms/ (header, sidebar, form)
templates/ (page layouts)
pages/     (specific implementations)
```

### 2. Container/Presentational Pattern
```typescript
// Container Component
const UserListContainer = () => {
  const { users, loading, error } = useUsers()
  
  if (loading) return <Spinner />
  if (error) return <ErrorMessage error={error} />
  
  return <UserList users={users} />
}

// Presentational Component
interface UserListProps {
  users: User[]
}

const UserList: React.FC<UserListProps> = ({ users }) => (
  <ul>
    {users.map(user => (
      <li key={user.id}>{user.name}</li>
    ))}
  </ul>
)
```

### 3. Compound Components
```typescript
const Tabs = ({ children }: { children: React.ReactNode }) => {
  const [activeTab, setActiveTab] = useState(0)
  
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      {children}
    </TabsContext.Provider>
  )
}

Tabs.Tab = ({ children, index }: TabProps) => {
  const { activeTab, setActiveTab } = useContext(TabsContext)
  
  return (
    <button 
      onClick={() => setActiveTab(index)}
      className={activeTab === index ? 'active' : ''}
    >
      {children}
    </button>
  )
}

Tabs.Panel = ({ children, index }: PanelProps) => {
  const { activeTab } = useContext(TabsContext)
  
  return activeTab === index ? <div>{children}</div> : null
}
```

## State Management Patterns

### 1. Local State
- Use useState for simple component state
- Use useReducer for complex state logic
- Consider useImmer for immutable updates

### 2. Global State
```typescript
// Zustand Example
const useStore = create((set) => ({
  user: null,
  login: (user) => set({ user }),
  logout: () => set({ user: null }),
}))

// Usage
const UserProfile = () => {
  const { user, logout } = useStore()
  // ...
}
```

### 3. Server State
```typescript
// React Query Example
const useUsers = () => {
  return useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers,
    staleTime: 5 * 60 * 1000, // 5 minutes
    cacheTime: 10 * 60 * 1000, // 10 minutes
  })
}
```

## Data Fetching Patterns

### 1. SWR Pattern
```typescript
function useSWR<T>(key: string, fetcher: () => Promise<T>) {
  const [data, setData] = useState<T | null>(null)
  const [error, setError] = useState<Error | null>(null)
  const [isLoading, setIsLoading] = useState(true)
  
  useEffect(() => {
    let cancelled = false
    
    const fetchData = async () => {
      try {
        const result = await fetcher()
        if (!cancelled) {
          setData(result)
          setError(null)
        }
      } catch (err) {
        if (!cancelled) {
          setError(err as Error)
        }
      } finally {
        if (!cancelled) {
          setIsLoading(false)
        }
      }
    }
    
    fetchData()
    
    return () => {
      cancelled = true
    }
  }, [key, fetcher])
  
  return { data, error, isLoading, mutate: fetchData }
}
```

### 2. Infinite Scroll
```typescript
const useInfiniteScroll = (fetchPage: (page: number) => Promise<any[]>) => {
  const [pages, setPages] = useState<any[][]>([])
  const [page, setPage] = useState(1)
  const [hasMore, setHasMore] = useState(true)
  const [loading, setLoading] = useState(false)
  
  const loadMore = useCallback(async () => {
    if (loading || !hasMore) return
    
    setLoading(true)
    try {
      const newPage = await fetchPage(page)
      setPages(prev => [...prev, newPage])
      setPage(prev => prev + 1)
      setHasMore(newPage.length > 0)
    } finally {
      setLoading(false)
    }
  }, [page, loading, hasMore, fetchPage])
  
  return {
    items: pages.flat(),
    loadMore,
    hasMore,
    loading
  }
}
```

## Performance Patterns

### 1. Code Splitting
```typescript
// Route-based splitting
const Home = lazy(() => import('./pages/Home'))
const About = lazy(() => import('./pages/About'))

// Component-based splitting
const HeavyComponent = lazy(() => import('./HeavyComponent'))

// Usage
<Suspense fallback={<Spinner />}>
  <HeavyComponent />
</Suspense>
```

### 2. Memoization
```typescript
// React.memo for components
const ExpensiveComponent = React.memo(({ data }: Props) => {
  return <div>{/* expensive rendering */}</div>
})

// useMemo for values
const ExpensiveValue = ({ items }: { items: Item[] }) => {
  const processed = useMemo(() => {
    return items.map(processItem).filter(Boolean)
  }, [items])
  
  return <div>{processed}</div>
}

// useCallback for functions
const Parent = ({ items }: { items: Item[] }) => {
  const handleClick = useCallback((id: string) => {
    // handle click
  }, [])
  
  return items.map(item => (
    <Child key={item.id} item={item} onClick={handleClick} />
  ))
}
```

### 3. Virtual Scrolling
```typescript
const VirtualList = ({ items, itemHeight, containerHeight }: VirtualListProps) => {
  const [scrollTop, setScrollTop] = useState(0)
  
  const visibleStart = Math.floor(scrollTop / itemHeight)
  const visibleEnd = Math.min(
    visibleStart + Math.ceil(containerHeight / itemHeight),
    items.length - 1
  )
  
  const visibleItems = items.slice(visibleStart, visibleEnd + 1)
  
  return (
    <div 
      style={{ height: containerHeight, overflow: 'auto' }}
      onScroll={(e) => setScrollTop(e.currentTarget.scrollTop)}
    >
      <div style={{ height: items.length * itemHeight, position: 'relative' }}>
        {visibleItems.map((item, index) => (
          <div
            key={item.id}
            style={{
              position: 'absolute',
              top: (visibleStart + index) * itemHeight,
              height: itemHeight,
              width: '100%'
            }}
          >
            <ItemComponent item={item} />
          </div>
        ))}
      </div>
    </div>
  )
}
```

## Form Patterns

### 1. Controlled Components
```typescript
const FormExample = () => {
  const [values, setValues] = useState({
    email: '',
    password: ''
  })
  const [errors, setErrors] = useState<Record<string, string>>({})
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target
    setValues(prev => ({ ...prev, [name]: value }))
    
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({ ...prev, [name]: '' }))
    }
  }
  
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    
    // Validation
    const newErrors: Record<string, string> = {}
    if (!values.email) newErrors.email = 'Email is required'
    if (!values.password) newErrors.password = 'Password is required'
    
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors)
      return
    }
    
    // Submit
    await submitForm(values)
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        type="email"
        value={values.email}
        onChange={handleChange}
      />
      {errors.email && <span className="error">{errors.email}</span>}
      
      <input
        name="password"
        type="password"
        value={values.password}
        onChange={handleChange}
      />
      {errors.password && <span className="error">{errors.password}</span>}
      
      <button type="submit">Submit</button>
    </form>
  )
}
```

### 2. Form Libraries (React Hook Form)
```typescript
const HookFormExample = () => {
  const {
    register,
    handleSubmit,
    formState: { errors }
  } = useForm()
  
  const onSubmit = (data: FormData) => {
    console.log(data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register('email', {
          required: 'Email is required',
          pattern: {
            value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
            message: 'Invalid email address'
          }
        })}
      />
      {errors.email && <span>{errors.email.message}</span>}
      
      <button type="submit">Submit</button>
    </form>
  )
}
```

## Styling Patterns

### 1. CSS-in-JS (Styled Components)
```typescript
const Button = styled.button<{ variant?: 'primary' | 'secondary' }>`
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
  
  ${props => props.variant === 'primary' && css`
    background: #007bff;
    color: white;
    
    &:hover {
      background: #0056b3;
    }
  `}
  
  ${props => props.variant === 'secondary' && css`
    background: transparent;
    color: #007bff;
    border: 1px solid #007bff;
    
    &:hover {
      background: #007bff;
      color: white;
    }
  `}
`
```

### 2. CSS Modules
```css
/* Button.module.css */
.button {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
}

.primary {
  background: #007bff;
  color: white;
}

.secondary {
  background: transparent;
  color: #007bff;
  border: 1px solid #007bff;
}
```

```typescript
import styles from './Button.module.css'

const Button = ({ variant = 'primary', children, ...props }) => (
  <button className={`${styles.button} ${styles[variant]}`} {...props}>
    {children}
  </button>
)
```

## Testing Patterns

### 1. Component Testing
```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { Button } from './Button'

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument()
  })
  
  it('calls onClick when clicked', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click me</Button>)
    
    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })
  
  it('applies variant styles', () => {
    render(<Button variant="secondary">Click me</Button>)
    expect(screen.getByRole('button')).toHaveClass('secondary')
  })
})
```

### 2. Integration Testing
```typescript
import { render, screen, waitFor } from '@testing-library/react'
import { UserForm } from './UserForm'
import userEvent from '@testing-library/user-event'

describe('UserForm Integration', () => {
  it('submits form with valid data', async () => {
    const mockSubmit = jest.fn()
    render(<UserForm onSubmit={mockSubmit} />)
    
    await userEvent.type(screen.getByLabelText(/name/i), 'John Doe')
    await userEvent.type(screen.getByLabelText(/email/i), 'john@example.com')
    await userEvent.click(screen.getByRole('button', { name: /submit/i }))
    
    await waitFor(() => {
      expect(mockSubmit).toHaveBeenCalledWith({
        name: 'John Doe',
        email: 'john@example.com'
      })
    })
  })
})
```

## Best Practices

1. **Component Composition**: Prefer composition over inheritance
2. **Prop Drilling**: Use context or state management for deep prop passing
3. **Side Effects**: Isolate side effects in custom hooks
4. **Error Boundaries**: Catch and handle errors gracefully
5. **Accessibility**: Ensure components are keyboard navigable and screen reader friendly
6. **Performance**: Profile before optimizing
7. **Testing**: Test behavior, not implementation
