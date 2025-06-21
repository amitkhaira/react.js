# React.js Cheat Sheet

## What is React & Why Use It?

React is a JavaScript library for building user interfaces, particularly web applications. It allows developers to create reusable UI components and manage application state efficiently through a virtual DOM system.

**Key Benefits:**
- **Component-based architecture** - Build encapsulated components that manage their own state
- **Virtual DOM** - Efficient updates and rendering for better performance
- **Declarative** - Describe what the UI should look like, not how to achieve it
- **Large ecosystem** - Extensive community support and third-party libraries
- **SEO-friendly** - Server-side rendering capabilities with Next.js
- **Developer tools** - Excellent debugging tools and hot reloading

## Installation & Setup

```bash
# Create new React app
npx create-react-app my-app
cd my-app
npm start

# With Vite (faster alternative)
npm create vite@latest my-app -- --template react

# With TypeScript
npx create-react-app my-app --template typescript
npm create vite@latest my-app -- --template react-ts
```

## File Structure & Organization

```
src/
├── components/           # Reusable UI components
│   ├── common/          # Shared components (Button, Modal, etc.)
│   └── specific/        # Feature-specific components
├── hooks/               # Custom hooks
├── context/             # Context providers
├── pages/               # Page components (for routing)
├── utils/               # Helper functions
├── services/            # API calls and external services
├── styles/              # Global styles and themes
├── types/               # TypeScript type definitions
└── __tests__/           # Test files
```

## Components

### Functional Components
### Custom Hooks
Extract reusable stateful logic into custom hooks for better code organization.

```jsx
// Basic functional component
function MyComponent() {
  return <div>Hello World</div>;
}

// Arrow function component
const MyComponent = () => {
  return <div>Hello World</div>;
};

// With props
const Greeting = ({ name, age }) => {
  return <h1>Hello {name}, you are {age} years old</h1>;
};
```

### Class Components (Legacy)
```jsx
class MyComponent extends React.Component {
  render() {
    return <div>Hello World</div>;
  }
}
```

## JSX Syntax

```jsx
// JSX expressions
const name = 'John';
const element = <h1>Hello, {name}!</h1>;

// Conditional rendering
const greeting = isLoggedIn ? <h1>Welcome!</h1> : <h1>Please log in</h1>;

// Lists
const items = ['apple', 'banana', 'orange'];
const listItems = items.map(item => <li key={item}>{item}</li>);

// Inline styles
const style = { color: 'red', fontSize: '16px' };
<div style={style}>Styled text</div>

// CSS classes
<div className="my-class">Content</div>
```

## Hooks

### useState
Hook for adding state to functional components. Returns current state value and setter function.

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(prev => prev - 1)}>-</button>
    </div>
  );
}
```

### useEffect
Hook for handling side effects like API calls, subscriptions, and DOM manipulation in functional components.

```jsx
import { useEffect, useState } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);
  
  // Run once on mount
  useEffect(() => {
    fetchData().then(setData);
  }, []);
  
  // Run when dependency changes
  useEffect(() => {
    console.log('Data changed:', data);
  }, [data]);
  
  // Cleanup function
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('Timer tick');
    }, 1000);
    
    return () => clearInterval(timer); // Cleanup
  }, []);
  
  return <div>{data ? JSON.stringify(data) : 'Loading...'}</div>;
}
```

### useContext
Hook for consuming context values without wrapping components in Consumer components.

```jsx
import { createContext, useContext } from 'react';

// Create context
const ThemeContext = createContext();

// Provider component
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <MyComponent />
    </ThemeContext.Provider>
  );
}

// Consume context
function MyComponent() {
  const theme = useContext(ThemeContext);
  return <div>Current theme: {theme}</div>;
}
```

### useReducer
Hook for managing complex state logic, similar to Redux but built into React.

```jsx
import { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}
```

### Advanced Hooks (React 18+)

#### useTransition & useDeferredValue
For handling expensive operations without blocking UI updates.

```jsx
import { useTransition, useDeferredValue, useState } from 'react';

function SearchResults() {
  const [query, setQuery] = useState('');
  const [isPending, startTransition] = useTransition();
  const deferredQuery = useDeferredValue(query);
  
  const handleInputChange = (e) => {
    // Urgent update - immediate
    setQuery(e.target.value);
    
    // Non-urgent update - can be deferred
    startTransition(() => {
      // Expensive search operation
      performSearch(e.target.value);
    });
  };
  
  return (
    <div>
      <input value={query} onChange={handleInputChange} />
      {isPending && <div>Searching...</div>}
      <SearchList query={deferredQuery} />
    </div>
  );
}
```

#### useId
Generates unique IDs for accessibility attributes.

```jsx
import { useId } from 'react';

function FormField({ label, ...props }) {
  const id = useId();
  
  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input id={id} {...props} />
    </div>
  );
}
```

#### useSyncExternalStore
For subscribing to external data sources.

```jsx
import { useSyncExternalStore } from 'react';

function useWindowWidth() {
  return useSyncExternalStore(
    (callback) => {
      window.addEventListener('resize', callback);
      return () => window.removeEventListener('resize', callback);
    },
    () => window.innerWidth,
    () => 0 // Server-side fallback
  );
}

function ResponsiveComponent() {
  const width = useWindowWidth();
  return <div>Window width: {width}px</div>;
}
```
```jsx
// Custom hook for local storage
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// Advanced custom hooks
function useFetch(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const abortController = new AbortController();
    
    const fetchData = async () => {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(url, {
          ...options,
          signal: abortController.signal
        });
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const result = await response.json();
        setData(result);
      } catch (err) {
        if (err.name !== 'AbortError') {
          setError(err);
        }
      } finally {
        setLoading(false);
      }
    };
    
    fetchData();
    
    return () => abortController.abort();
  }, [url, JSON.stringify(options)]);
  
  return { data, loading, error };
}

// Usage
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  
  return <div>Hello, {user.name}!</div>;
}
```

## React Router (Navigation)

```bash
npm install react-router-dom
```

### Basic Routing Setup
```jsx
import { BrowserRouter, Routes, Route, Link, Navigate } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
      </nav>
      
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users" element={<Users />} />
        <Route path="/users/:id" element={<UserDetail />} />
        <Route path="/admin/*" element={<AdminRoutes />} />
        <Route path="/login" element={<Login />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Advanced Routing Features
```jsx
import { useParams, useNavigate, useLocation, useSearchParams } from 'react-router-dom';

function UserDetail() {
  const { id } = useParams(); // Get URL parameters
  const navigate = useNavigate(); // Programmatic navigation
  const location = useLocation(); // Current location object
  const [searchParams, setSearchParams] = useSearchParams(); // Query parameters
  
  const goBack = () => navigate(-1);
  const goToEdit = () => navigate(`/users/${id}/edit`);
  
  return (
    <div>
      <h1>User {id}</h1>
      <p>Current path: {location.pathname}</p>
      <p>Tab: {searchParams.get('tab') || 'profile'}</p>
      <button onClick={goBack}>Go Back</button>
      <button onClick={goToEdit}>Edit User</button>
    </div>
  );
}

// Protected Routes
function ProtectedRoute({ children }) {
  const isAuthenticated = useAuth(); // Custom hook
  
  return isAuthenticated ? children : <Navigate to="/login" replace />;
}

// Usage
<Route 
  path="/dashboard" 
  element={
    <ProtectedRoute>
      <Dashboard />
    </ProtectedRoute>
  } 
/>
```

## Styling in React

### CSS Modules
```jsx
// Button.module.css
.button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
}

.primary {
  background-color: blue;
  color: white;
}

// Button.jsx
import styles from './Button.module.css';

function Button({ variant = 'primary', children, ...props }) {
  return (
    <button 
      className={`${styles.button} ${styles[variant]}`}
      {...props}
    >
      {children}
    </button>
  );
}
```

### Styled Components
```bash
npm install styled-components
```

```jsx
import styled, { css } from 'styled-components';

const Button = styled.button`
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  
  ${props => props.primary && css`
    background-color: blue;
    color: white;
  `}
  
  ${props => props.size === 'large' && css`
    padding: 15px 30px;
    font-size: 18px;
  `}
  
  &:hover {
    opacity: 0.8;
  }
`;

// Usage
<Button primary size="large">Primary Button</Button>
```

## State Management

### Context API for Global State
```jsx
// contexts/AppContext.js
import { createContext, useContext, useReducer } from 'react';

const AppContext = createContext();

const initialState = {
  user: null,
  theme: 'light',
  notifications: []
};

function appReducer(state, action) {
  switch (action.type) {
    case 'SET_USER':
      return { ...state, user: action.payload };
    case 'TOGGLE_THEME':
      return { ...state, theme: state.theme === 'light' ? 'dark' : 'light' };
    case 'ADD_NOTIFICATION':
      return { 
        ...state, 
        notifications: [...state.notifications, action.payload] 
      };
    case 'REMOVE_NOTIFICATION':
      return {
        ...state,
        notifications: state.notifications.filter(n => n.id !== action.payload)
      };
    default:
      return state;
  }
}

export function AppProvider({ children }) {
  const [state, dispatch] = useReducer(appReducer, initialState);
  
  return (
    <AppContext.Provider value={{ state, dispatch }}>
      {children}
    </AppContext.Provider>
  );
}

export function useAppContext() {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useAppContext must be used within AppProvider');
  }
  return context;
}
```

### Redux Toolkit (Modern Redux)
```bash
npm install @reduxjs/toolkit react-redux
```

```jsx
// store/userSlice.js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

export const fetchUser = createAsyncThunk(
  'user/fetchUser',
  async (userId, { rejectWithValue }) => {
    try {
      const response = await fetch(`/api/users/${userId}`);
      return await response.json();
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

const userSlice = createSlice({
  name: 'user',
  initialState: {
    data: null,
    loading: false,
    error: null
  },
  reducers: {
    clearUser: (state) => {
      state.data = null;
    },
    updateProfile: (state, action) => {
      if (state.data) {
        state.data = { ...state.data, ...action.payload };
      }
    }
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.loading = false;
        state.data = action.payload;
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.loading = false;
        state.error = action.payload;
      });
  }
});

export const { clearUser, updateProfile } = userSlice.actions;
export default userSlice.reducer;

// store/index.js
import { configureStore } from '@reduxjs/toolkit';
import userReducer from './userSlice';

export const store = configureStore({
  reducer: {
    user: userReducer
  }
});

// Usage in component
import { useSelector, useDispatch } from 'react-redux';
import { fetchUser, updateProfile } from './store/userSlice';

function UserProfile() {
  const dispatch = useDispatch();
  const { data: user, loading, error } = useSelector(state => state.user);
  
  useEffect(() => {
    dispatch(fetchUser(123));
  }, [dispatch]);
  
  const handleUpdate = (updates) => {
    dispatch(updateProfile(updates));
  };
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return <div>{user?.name}</div>;
}
```

## Testing React Components

```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

### Basic Component Testing
```jsx
// Button.test.jsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import '@testing-library/jest-dom';
import Button from './Button';

describe('Button Component', () => {
  test('renders button with text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });
  
  test('calls onClick handler when clicked', async () => {
    const user = userEvent.setup();
    const handleClick = jest.fn();
    
    render(<Button onClick={handleClick}>Click me</Button>);
    
    await user.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  
  test('applies correct CSS class for variant', () => {
    render(<Button variant="primary">Primary</Button>);
    expect(screen.getByRole('button')).toHaveClass('primary');
  });
  
  test('is disabled when disabled prop is true', () => {
    render(<Button disabled>Disabled</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });
});
```

### Testing Hooks
```jsx
// useCounter.test.js
import { renderHook, act } from '@testing-library/react';
import useCounter from './useCounter';

describe('useCounter Hook', () => {
  test('should initialize with default value', () => {
    const { result } = renderHook(() => useCounter());
    expect(result.current.count).toBe(0);
  });
  
  test('should initialize with provided value', () => {
    const { result } = renderHook(() => useCounter(10));
    expect(result.current.count).toBe(10);
  });
  
  test('should increment count', () => {
    const { result } = renderHook(() => useCounter());
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});
```

### Testing with Context
```jsx
// renderWithContext.js (test utility)
import { render } from '@testing-library/react';
import { AppProvider } from '../contexts/AppContext';

function renderWithContext(ui, options = {}) {
  const Wrapper = ({ children }) => (
    <AppProvider>{children}</AppProvider>
  );
  
  return render(ui, { wrapper: Wrapper, ...options });
}

// Usage in tests
test('displays user name from context', () => {
  renderWithContext(<UserProfile />);
  expect(screen.getByText(/john doe/i)).toBeInTheDocument();
});
```

## TypeScript with React

### Component Types
```tsx
// types/index.ts
export interface User {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user';
}

export interface ApiResponse<T> {
  data: T;
  status: 'success' | 'error';
  message?: string;
}

// Components with TypeScript
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'small' | 'medium' | 'large';
  loading?: boolean;
  children: React.ReactNode;
}

const Button: React.FC<ButtonProps> = ({ 
  variant = 'primary', 
  size = 'medium',
  loading = false,
  children,
  disabled,
  ...props 
}) => {
  return (
    <button 
      disabled={disabled || loading}
      className={`btn btn-${variant} btn-${size}`}
      {...props}
    >
      {loading ? 'Loading...' : children}
    </button>
  );
};

// Generic Components
interface ListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => React.ReactNode;
  keyExtractor: (item: T) => string | number;
  emptyMessage?: string;
}

function List<T>({ items, renderItem, keyExtractor, emptyMessage }: ListProps<T>) {
  if (items.length === 0) {
    return <div>{emptyMessage || 'No items found'}</div>;
  }
  
  return (
    <ul>
      {items.map((item, index) => (
        <li key={keyExtractor(item)}>
          {renderItem(item, index)}
        </li>
      ))}
    </ul>
  );
}

// Hook Types
function useApi<T>(url: string): {
  data: T | null;
  loading: boolean;
  error: string | null;
  refetch: () => Promise<void>;
} {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  
  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      const response = await fetch(url);
      const result: ApiResponse<T> = await response.json();
      setData(result.data);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'An error occurred');
    } finally {
      setLoading(false);
    }
  }, [url]);
  
  useEffect(() => {
    fetchData();
  }, [fetchData]);
  
  return { data, loading, error, refetch: fetchData };
}
```

## React Suspense & Lazy Loading

```jsx
import { Suspense, lazy } from 'react';

// Lazy load components
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Profile = lazy(() => import('./pages/Profile'));
const Settings = lazy(() => import('./pages/Settings'));

// Loading fallback component
const LoadingSpinner = () => (
  <div className="loading-spinner">
    <div>Loading...</div>
  </div>
);

function App() {
  return (
    <Router>
      <Suspense fallback={<LoadingSpinner />}>
        <Routes>
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/profile" element={<Profile />} />
          <Route path="/settings" element={<Settings />} />
        </Routes>
      </Suspense>
    </Router>
  );
}

// Advanced Suspense with Error Boundary
function AppWithSuspense() {
  return (
    <ErrorBoundary fallback={<ErrorPage />}>
      <Suspense fallback={<LoadingSpinner />}>
        <Router>
          <Routes>
            <Route path="/*" element={<MainApp />} />
          </Routes>
        </Router>
      </Suspense>
    </ErrorBoundary>
  );
}
```

## Build & Deployment

### Environment Variables
```bash
# .env.local
REACT_APP_API_URL=http://localhost:3001/api
REACT_APP_ENVIRONMENT=development
REACT_APP_ANALYTICS_ID=GA-XXXXXXX
```

```jsx
// Using environment variables
const apiUrl = process.env.REACT_APP_API_URL;
const isDevelopment = process.env.NODE_ENV === 'development';

if (isDevelopment) {
  console.log('Running in development mode');
}
```

### Performance Optimization for Production
```jsx
// webpack.config.js optimizations
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all',
        },
      },
    },
  },
};

// Bundle analyzer
npm install --save-dev webpack-bundle-analyzer
npm run build
npx webpack-bundle-analyzer build/static/js/*.js
```

### Deployment Commands
```bash
# Build for production
npm run build

# Serve production build locally
npm install -g serve
serve -s build

# Deploy to various platforms
# Netlify
netlify deploy --prod --dir=build

# Vercel
vercel --prod

# GitHub Pages
npm install --save-dev gh-pages
# Add to package.json scripts:
# "predeploy": "npm run build",
# "deploy": "gh-pages -d build"
npm run deploy
```

## Event Handling

```jsx
function Button() {
  const handleClick = (e) => {
    e.preventDefault();
    console.log('Button clicked!');
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    const formData = new FormData(e.target);
    console.log(Object.fromEntries(formData));
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input name="username" placeholder="Username" />
      <button type="submit" onClick={handleClick}>
        Submit
      </button>
    </form>
  );
}
```

## Forms & Controlled Components

```jsx
function LoginForm() {
  const [formData, setFormData] = useState({
    username: '',
    password: '',
    remember: false
  });
  
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form data:', formData);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        name="username"
        value={formData.username}
        onChange={handleChange}
        placeholder="Username"
      />
      <input
        name="password"
        type="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Password"
      />
      <label>
        <input
          name="remember"
          type="checkbox"
          checked={formData.remember}
          onChange={handleChange}
        />
        Remember me
      </label>
      <button type="submit">Login</button>
    </form>
  );
}
```

## Props & PropTypes

```jsx
import PropTypes from 'prop-types';

function UserCard({ name, age, email, isActive = false }) {
  return (
    <div className={`user-card ${isActive ? 'active' : ''}`}>
      <h3>{name}</h3>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}

UserCard.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number.isRequired,
  email: PropTypes.string.isRequired,
  isActive: PropTypes.bool
};

// Usage
<UserCard name="John" age={30} email="john@example.com" isActive={true} />
```

## Conditional Rendering

```jsx
function ConditionalExample({ user, isLoading, error }) {
  // Early return
  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!user) return <div>No user found</div>;
  
  return (
    <div>
      {/* Ternary operator */}
      {user.isAdmin ? <AdminPanel /> : <UserPanel />}
      
      {/* Logical AND */}
      {user.notifications.length > 0 && (
        <NotificationBadge count={user.notifications.length} />
      )}
      
      {/* Switch case with object */}
      {(() => {
        const statusComponents = {
          active: <ActiveStatus />,
          inactive: <InactiveStatus />,
          pending: <PendingStatus />
        };
        return statusComponents[user.status] || <UnknownStatus />;
      })()}
    </div>
  );
}
```

## List Rendering

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo, index) => (
        <li key={todo.id || index} className={todo.completed ? 'completed' : ''}>
          {todo.text}
        </li>
      ))}
    </ul>
  );
}

// Filtering and sorting
function FilteredList({ items, filter }) {
  const filteredItems = items
    .filter(item => item.name.toLowerCase().includes(filter.toLowerCase()))
    .sort((a, b) => a.name.localeCompare(b.name));
    
  return (
    <div>
      {filteredItems.length === 0 ? (
        <p>No items found</p>
      ) : (
        <ul>
          {filteredItems.map(item => (
            <li key={item.id}>{item.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

## Performance Optimization

### React.memo
```jsx
import { memo } from 'react';

const ExpensiveComponent = memo(function ExpensiveComponent({ data, onUpdate }) {
  console.log('ExpensiveComponent rendered');
  return <div>{data.name}</div>;
});

// Custom comparison function
const MemoizedComponent = memo(function MyComponent({ user, settings }) {
  return <div>{user.name}</div>;
}, (prevProps, nextProps) => {
  return prevProps.user.id === nextProps.user.id;
});
```

### useMemo & useCallback
```jsx
import { useMemo, useCallback, useState } from 'react';

function OptimizedComponent({ items, multiplier }) {
  const [filter, setFilter] = useState('');
  
  // Memoize expensive calculations
  const expensiveValue = useMemo(() => {
    return items.reduce((sum, item) => sum + item.value * multiplier, 0);
  }, [items, multiplier]);
  
  // Memoize callbacks
  const handleFilterChange = useCallback((e) => {
    setFilter(e.target.value);
  }, []);
  
  const filteredItems = useMemo(() => {
    return items.filter(item => 
      item.name.toLowerCase().includes(filter.toLowerCase())
    );
  }, [items, filter]);
  
  return (
    <div>
      <input value={filter} onChange={handleFilterChange} />
      <p>Total value: {expensiveValue}</p>
      <ItemList items={filteredItems} />
    </div>
  );
}
```

## Error Boundaries

```jsx
import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    this.setState({
      error: error,
      errorInfo: errorInfo
    });
  }
  
  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h2>Something went wrong</h2>
          {this.props.showDetails && (
            <details style={{ whiteSpace: 'pre-wrap' }}>
              {this.state.error && this.state.error.toString()}
              <br />
              {this.state.errorInfo.componentStack}
            </details>
          )}
        </div>
      );
    }
    
    return this.props.children;
  }
}

// Usage
<ErrorBoundary showDetails={process.env.NODE_ENV === 'development'}>
  <MyComponent />
</ErrorBoundary>
```

## Refs

```jsx
import { useRef, useEffect } from 'react';

function RefExample() {
  const inputRef = useRef(null);
  const countRef = useRef(0);
  
  useEffect(() => {
    // Focus input on mount
    inputRef.current.focus();
  }, []);
  
  const handleClick = () => {
    countRef.current += 1;
    console.log('Click count:', countRef.current);
  };
  
  return (
    <div>
      <input ref={inputRef} placeholder="This will be focused" />
      <button onClick={handleClick}>Click me</button>
    </div>
  );
}
```

## Common Patterns

### Higher-Order Components (HOC)
```jsx
function withLoading(WrappedComponent) {
  return function WithLoadingComponent({ isLoading, ...props }) {
    if (isLoading) {
      return <div>Loading...</div>;
    }
    return <WrappedComponent {...props} />;
  };
}

// Usage
const MyComponentWithLoading = withLoading(MyComponent);
<MyComponentWithLoading isLoading={true} data={data} />
```

### Render Props
```jsx
function DataFetcher({ render, url }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => {
        setData(data);
        setLoading(false);
      });
  }, [url]);
  
  return render({ data, loading });
}

// Usage
<DataFetcher
  url="/api/users"
  render={({ data, loading }) => (
    loading ? <div>Loading...</div> : <UserList users={data} />
  )}
/>
```

## Common Mistakes to Avoid

### 1. Mutating State Directly
```jsx
// ❌ Wrong - mutating state directly
const [items, setItems] = useState([1, 2, 3]);
items.push(4); // This won't trigger re-render

// ✅ Correct - create new array
setItems([...items, 4]);
setItems(prev => [...prev, 4]); // Even better with functional update
```

### 2. Missing Dependencies in useEffect
```jsx
// ❌ Wrong - missing dependency
useEffect(() => {
  fetchData(userId);
}, []); // userId should be in dependency array

// ✅ Correct - include all dependencies
useEffect(() => {
  fetchData(userId);
}, [userId]);
```

### 3. Using Array Index as Key
```jsx
// ❌ Wrong - using index as key
{items.map((item, index) => (
  <Item key={index} data={item} />
))}

// ✅ Correct - use unique, stable identifiers
{items.map(item => (
  <Item key={item.id} data={item} />
))}
```

### 4. Forgetting to Bind Event Handlers
```jsx
// ❌ Wrong - creating new function on every render
<button onClick={() => handleClick(item.id)}>Click</button>

// ✅ Better - use useCallback for complex handlers
const handleClick = useCallback((id) => {
  // handle click logic
}, []);

<button onClick={() => handleClick(item.id)}>Click</button>
```

### 5. Not Cleaning Up Effects
```jsx
// ❌ Wrong - memory leak potential
useEffect(() => {
  const interval = setInterval(() => {
    console.log('tick');
  }, 1000);
}, []);

// ✅ Correct - cleanup function
useEffect(() => {
  const interval = setInterval(() => {
    console.log('tick');
  }, 1000);
  
  return () => clearInterval(interval);
}, []);
```

## Best Practices

### 1. Component Organization
- Keep components small and focused on a single responsibility
- Use descriptive names for components and props
- Extract reusable logic into custom hooks
- Prefer functional components over class components

### 2. State Management
- Keep state as close to where it's used as possible
- Lift state up only when necessary for sharing between components
- Use useReducer for complex state logic
- Consider state management libraries (Redux, Zustand) for large applications

### 3. Performance Optimization
- Use React.memo for expensive components that receive the same props
- Optimize expensive calculations with useMemo
- Memoize callback functions with useCallback
- Lazy load components with React.Suspense and dynamic imports

### 4. Code Structure
```jsx
// ✅ Good component structure
function MyComponent({ data, onUpdate }) {
  // 1. Hooks at the top
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  // 2. Derived state and memoized values
  const sortedData = useMemo(() => 
    data.sort((a, b) => a.name.localeCompare(b.name)), [data]
  );
  
  // 3. Event handlers
  const handleSubmit = useCallback((formData) => {
    setLoading(true);
    onUpdate(formData)
      .finally(() => setLoading(false));
  }, [onUpdate]);
  
  // 4. Effects
  useEffect(() => {
    // side effects
  }, []);
  
  // 5. Early returns for loading/error states
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;
  if (!data?.length) return <EmptyState />;
  
  // 6. Main render
  return (
    <div>
      {/* JSX content */}
    </div>
  );
}
```

### 5. Props and PropTypes
- Use destructuring for props to make dependencies clear
- Provide default values for optional props
- Use PropTypes or TypeScript for type checking
- Keep prop drilling shallow (max 2-3 levels)

### 6. Testing Best Practices
- Write tests for component behavior, not implementation details
- Test user interactions and edge cases
- Use React Testing Library for component testing
- Mock external dependencies and API calls

### 7. Accessibility
- Use semantic HTML elements
- Provide proper ARIA labels and roles
- Ensure keyboard navigation works
- Test with screen readers
- Maintain proper color contrast ratios

## Key Concepts

- **Virtual DOM**: React creates a virtual representation of the DOM for efficient updates
- **One-way Data Flow**: Data flows down from parent to child components
- **Immutability**: Always create new objects/arrays instead of modifying existing ones
- **Keys**: Use unique keys for list items to help React identify changes
- **Lifting State Up**: Move state to the closest common ancestor when sharing between components
- **Composition over Inheritance**: Use composition to build complex components from simpler ones

## Learning Path by Skill Level

### 🟢 Beginner (Start Here)
**Essential concepts to master first:**
- Components (functional vs class)
- JSX syntax and expressions
- Props and state (useState)
- Event handling
- Conditional rendering
- Lists and keys
- Basic forms

**Practice Projects:**
- Todo List App
- Weather App
- Simple Calculator
- Contact List

### 🟡 Intermediate (Build on Basics)
**Next concepts to learn:**
- useEffect and lifecycle
- Custom hooks
- Context API
- Error boundaries
- React Router basics
- Form validation
- Performance optimization (memo, useMemo, useCallback)

**Practice Projects:**
- Blog with routing
- E-commerce product catalog
- User authentication system
- Real-time chat app

### 🔴 Advanced (Master React)
**Advanced concepts:**
- Concurrent features (useTransition, useDeferredValue)
- Advanced patterns (HOCs, render props, compound components)
- State management (Redux, Zustand)
- Testing strategies
- TypeScript integration
- Server-side rendering (Next.js)
- Micro-frontends

**Practice Projects:**
- Full-stack application
- Design system/component library
- Real-time collaborative tool
- Performance-critical dashboard

## Common Commands

```bash
# Development
npm start          # Start development server
npm test           # Run tests
npm run build      # Build for production
npm run eject      # Eject from Create React App (irreversible)

# Package management
npm install package-name
npm uninstall package-name
npm update package-name
```
