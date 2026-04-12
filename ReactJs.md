# React.js Interview Topics: Basic to Advanced

---

## TABLE OF CONTENTS

1. [What is React?](#1-what-is-react)
2. [JSX — JavaScript XML](#2-jsx--javascript-xml)
3. [Components — Functional & Class](#3-components--functional--class)
4. [Props](#4-props)
5. [State](#5-state)
6. [Event Handling](#6-event-handling)
7. [Conditional Rendering](#7-conditional-rendering)
8. [Lists & Keys](#8-lists--keys)
9. [Forms & Controlled Components](#9-forms--controlled-components)
10. [Component Lifecycle](#10-component-lifecycle)
11. [React Hooks — Core](#11-react-hooks--core)
12. [React Hooks — Advanced](#12-react-hooks--advanced)
13. [Context API](#13-context-api)
14. [useReducer & State Management](#14-usereducer--state-management)
15. [React Router](#15-react-router)
16. [Code Splitting & Lazy Loading](#16-code-splitting--lazy-loading)
17. [Error Boundaries](#17-error-boundaries)
18. [Refs & the DOM](#18-refs--the-dom)
19. [Higher-Order Components (HOC)](#19-higher-order-components-hoc)
20. [Render Props Pattern](#20-render-props-pattern)
21. [Custom Hooks](#21-custom-hooks)
22. [Performance Optimization](#22-performance-optimization)
23. [React.memo, useMemo & useCallback](#23-reactmemo-usememo--usecallback)
24. [Virtual DOM & Reconciliation](#24-virtual-dom--reconciliation)
25. [State Management — Redux](#25-state-management--redux)
26. [Redux Toolkit (RTK)](#26-redux-toolkit-rtk)
27. [React Query (TanStack Query)](#27-react-query-tanstack-query)
28. [Testing in React](#28-testing-in-react)
29. [Styling in React](#29-styling-in-react)
30. [Server-Side Rendering & Next.js](#30-server-side-rendering--nextjs)
31. [React Patterns & Best Practices](#31-react-patterns--best-practices)
32. [React 18 — New Features](#32-react-18--new-features)

---

## 1. What is React?

### Definition
React is an **open-source JavaScript library** developed by Facebook (Meta) for building **user interfaces**, particularly Single Page Applications (SPAs). It uses a **component-based architecture** and a **declarative programming model**.

### Key Characteristics

**Declarative:**
You describe what the UI should look like for a given state, and React handles the DOM updates.
```jsx
// Declarative — describe the "what"
const Button = ({ isActive }) => (
  <button className={isActive ? 'active' : 'inactive'}>
    {isActive ? 'Active' : 'Inactive'}
  </button>
);
```

**Component-Based:**
UI is split into independent, reusable pieces called components.

**Unidirectional Data Flow:**
Data flows from parent → child via props. Child components cannot directly modify parent state.

**Virtual DOM:**
React maintains a lightweight copy of the real DOM in memory. On state change, it computes the diff and updates only what changed.

### React vs Other Frameworks

| Feature | React | Angular | Vue |
|---------|-------|---------|-----|
| Type | Library (UI only) | Full framework | Progressive framework |
| Language | JavaScript/JSX | TypeScript | JavaScript/Vue SFC |
| Data binding | One-way | Two-way | Two-way |
| Learning curve | Medium | Steep | Low |
| Bundle size | Small | Large | Small |
| Flexibility | Very high | Low (opinionated) | Medium |

### Interview Q&A
**Q: Is React a framework or a library?**
> React is a library — it only handles the View layer (UI rendering). For a full application you compose it with other libraries like React Router (routing), Redux (state), Axios (HTTP). Angular, by contrast, is a full framework that includes all of these out of the box.

**Q: What are the advantages of React?**
> Virtual DOM for efficient updates, reusable component architecture, rich ecosystem, strong community, declarative code is easier to reason about, React Native enables mobile development with the same skills.

---

## 2. JSX — JavaScript XML

### What is JSX?
JSX is a **syntax extension for JavaScript** that looks like HTML but is transformed into `React.createElement()` calls by Babel.

```jsx
// JSX
const element = <h1 className="title">Hello, World!</h1>;

// What Babel compiles it to:
const element = React.createElement(
  'h1',
  { className: 'title' },
  'Hello, World!'
);
```

### JSX Rules

**1. Return a single root element:**
```jsx
// Wrong — multiple root elements
return (
  <h1>Title</h1>
  <p>Paragraph</p>
);

// Correct — wrapped in a parent or Fragment
return (
  <div>
    <h1>Title</h1>
    <p>Paragraph</p>
  </div>
);

// Correct — using Fragment (no extra DOM node)
return (
  <>
    <h1>Title</h1>
    <p>Paragraph</p>
  </>
);
```

**2. All tags must be closed:**
```jsx
// Wrong
<input type="text">
<img src="photo.jpg">

// Correct
<input type="text" />
<img src="photo.jpg" />
```

**3. Use `className` instead of `class`, `htmlFor` instead of `for`:**
```jsx
<div className="container">
  <label htmlFor="email">Email</label>
  <input id="email" type="email" />
</div>
```

**4. JavaScript expressions in curly braces `{}`:**
```jsx
const name = 'Alice';
const age = 25;

return (
  <div>
    <h1>Hello, {name}!</h1>
    <p>Age: {age}</p>
    <p>Adult: {age >= 18 ? 'Yes' : 'No'}</p>
    <p>Upper: {name.toUpperCase()}</p>
  </div>
);
```

**5. Style is an object (camelCase properties):**
```jsx
const style = { backgroundColor: 'blue', fontSize: '16px' };

<div style={style}>Styled</div>
// or inline:
<div style={{ color: 'red', marginTop: 10 }}>Styled</div>
```

**6. Comments in JSX:**
```jsx
return (
  <div>
    {/* This is a JSX comment */}
    <p>Content</p>
  </div>
);
```

### Interview Q&A
**Q: Can browsers read JSX directly?**
> No. Browsers only understand JavaScript. JSX must be transpiled to `React.createElement()` calls using Babel before the browser can execute it.

**Q: What is a Fragment and why use it?**
> A Fragment (`<>...</>` or `<React.Fragment>`) lets you group multiple elements without adding an extra DOM node. This keeps the DOM clean and avoids unnecessary wrapper divs.

---

## 3. Components — Functional & Class

### Functional Components (Modern Standard)

A plain JavaScript function that accepts props and returns JSX.

```jsx
// Arrow function
const Greeting = ({ name, age }) => {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Age: {age}</p>
    </div>
  );
};

// Regular function
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Usage
<Greeting name="Alice" age={25} />
```

### Class Components (Legacy)

ES6 classes extending `React.Component`. Required before hooks (React 16.8).

```jsx
import React, { Component } from 'react';

class Greeting extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  render() {
    const { name } = this.props;
    const { count } = this.state;
    return (
      <div>
        <h1>Hello, {name}!</h1>
        <p>Count: {count}</p>
        <button onClick={() => this.setState({ count: count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}
```

### Functional vs Class Components

| Feature | Functional | Class |
|---------|-----------|-------|
| Syntax | Simple function | ES6 class |
| State | useState hook | this.state |
| Lifecycle | useEffect hook | Lifecycle methods |
| `this` keyword | Not needed | Required |
| Performance | Slightly better | Slightly more overhead |
| Code length | Shorter | Longer |
| Modern React | Preferred | Legacy |

### Component Composition

```jsx
// Building complex UI from small, reusable components
const Avatar = ({ src, alt }) => <img src={src} alt={alt} className="avatar" />;

const UserInfo = ({ user }) => (
  <div className="user-info">
    <Avatar src={user.photo} alt={user.name} />
    <span>{user.name}</span>
  </div>
);

const Comment = ({ comment }) => (
  <div className="comment">
    <UserInfo user={comment.author} />
    <p>{comment.text}</p>
  </div>
);
```

### Interview Q&A
**Q: Why are functional components preferred over class components?**
> Functional components are simpler, shorter, and easier to test. Hooks provide all the functionality previously exclusive to class components (state, lifecycle, context). They also avoid confusion with `this` binding and make logic reuse easier through custom hooks.

---

## 4. Props

### What are Props?
Props (Properties) are **read-only inputs** passed from a parent component to a child component. They are immutable — a child cannot modify its own props.

```jsx
// Parent passing props
const App = () => (
  <UserCard
    name="Alice"
    age={25}
    isAdmin={true}
    hobbies={['reading', 'coding']}
    address={{ city: 'Mumbai' }}
    onClick={() => console.log('clicked')}
  />
);

// Child receiving props
const UserCard = ({ name, age, isAdmin, hobbies, address, onClick }) => (
  <div onClick={onClick}>
    <h2>{name} {isAdmin && '(Admin)'}</h2>
    <p>Age: {age}</p>
    <p>City: {address.city}</p>
    <ul>
      {hobbies.map(hobby => <li key={hobby}>{hobby}</li>)}
    </ul>
  </div>
);
```

### Default Props

```jsx
const Button = ({ label, color, size }) => (
  <button style={{ backgroundColor: color, fontSize: size }}>
    {label}
  </button>
);

// Default values using destructuring defaults
const Button = ({ label = 'Click Me', color = 'blue', size = '16px' }) => (
  <button style={{ backgroundColor: color, fontSize: size }}>
    {label}
  </button>
);
```

### children Prop

```jsx
const Card = ({ title, children }) => (
  <div className="card">
    <h2>{title}</h2>
    <div className="card-body">{children}</div>
  </div>
);

// Usage
<Card title="My Card">
  <p>This is the card content</p>
  <button>Action</button>
</Card>
```

### Prop Drilling Problem

```jsx
// Problem: Passing props through many levels
App → Dashboard → Sidebar → UserInfo → Avatar

// Solution: Context API or State Management (Redux)
```

### Interview Q&A
**Q: Can you modify props inside a child component?**
> No. Props are read-only (immutable). React enforces a unidirectional data flow. If a child needs to update data, it should call a callback function passed as a prop from the parent.

**Q: What is the difference between props and state?**
> Props are external inputs passed to a component (controlled by the parent). State is internal data managed by the component itself that can change over time.

---

## 5. State

### What is State?
State is **internal data** managed by a component that, when changed, triggers a re-render of the component.

```jsx
import { useState } from 'react';

const Counter = () => {
  const [count, setCount] = useState(0); // [currentValue, setter]

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
};
```

### State with Objects

```jsx
const [user, setUser] = useState({
  name: 'Alice',
  email: 'alice@example.com',
  age: 25
});

// WRONG — directly mutating state
user.name = 'Bob'; // Never do this!

// CORRECT — spread to create new object
setUser(prev => ({ ...prev, name: 'Bob' }));
```

### State with Arrays

```jsx
const [items, setItems] = useState(['Apple', 'Banana']);

// Add item
setItems(prev => [...prev, 'Cherry']);

// Remove item
setItems(prev => prev.filter(item => item !== 'Banana'));

// Update item
setItems(prev => prev.map(item => item === 'Apple' ? 'Avocado' : item));
```

### Functional Updates (Important!)

Use the function form when new state depends on previous state:

```jsx
// WRONG — may use stale state in batched updates
setCount(count + 1);
setCount(count + 1); // Both use same stale 'count'

// CORRECT — always gets latest value
setCount(prev => prev + 1);
setCount(prev => prev + 1); // Both work correctly
```

### State Lifting

Move state up to the nearest common ancestor when multiple components need the same state.

```jsx
// Parent manages shared state
const App = () => {
  const [selectedItem, setSelectedItem] = useState(null);

  return (
    <>
      <ItemList onSelect={setSelectedItem} />
      <ItemDetail item={selectedItem} />
    </>
  );
};
```

### Interview Q&A
**Q: Why should you never mutate state directly in React?**
> React uses reference comparison to detect state changes. If you mutate an object/array directly (same reference), React won't detect the change and won't re-render. Always return a new object/array.

**Q: What triggers a re-render in React?**
> State changes (via setState/useState setter), prop changes from the parent, Context value changes, and `forceUpdate()` (class components only).

---

## 6. Event Handling

### Synthetic Events
React wraps browser native events in a **SyntheticEvent** for cross-browser consistency.

```jsx
const handleClick = (event) => {
  console.log(event.target);      // DOM element
  console.log(event.type);        // 'click'
  event.preventDefault();         // Prevent default behavior
  event.stopPropagation();        // Stop event bubbling
};

// Common events
<button onClick={handleClick}>Click</button>
<input onChange={(e) => console.log(e.target.value)} />
<form onSubmit={handleSubmit}>...</form>
<input onKeyDown={(e) => console.log(e.key)} />
<div onMouseEnter={handleHover} onMouseLeave={handleLeave} />
<input onFocus={handleFocus} onBlur={handleBlur} />
```

### Passing Arguments to Event Handlers

```jsx
// Using arrow function (creates new function on each render)
<button onClick={() => handleDelete(item.id)}>Delete</button>

// Using data attributes (avoids new function per render)
<button data-id={item.id} onClick={handleDelete}>Delete</button>

const handleDelete = (e) => {
  const id = e.currentTarget.dataset.id;
  deleteItem(id);
};
```

### Event Delegation
React attaches a single event listener at the root, not on every element. It uses event bubbling to handle events efficiently.

### Interview Q&A
**Q: What is the difference between `event.target` and `event.currentTarget`?**
> `event.target` is the element that triggered the event. `event.currentTarget` is the element the event handler is attached to. They differ when event bubbling occurs.

---

## 7. Conditional Rendering

### Methods for Conditional Rendering

```jsx
const UserPanel = ({ isLoggedIn, isAdmin, notifications }) => {
  // 1. if/else (outside JSX)
  if (!isLoggedIn) {
    return <LoginPage />;
  }

  return (
    <div>
      {/* 2. Ternary operator */}
      {isAdmin ? <AdminDashboard /> : <UserDashboard />}

      {/* 3. Logical AND (&&) — renders if truthy */}
      {isAdmin && <AdminPanel />}

      {/* 4. Nullish coalescing for defaults */}
      <span>{notifications ?? 'No notifications'}</span>

      {/* 5. IIFE for complex logic inside JSX */}
      {(() => {
        if (notifications > 10) return <span>10+ alerts</span>;
        if (notifications > 0) return <span>{notifications} alerts</span>;
        return <span>No alerts</span>;
      })()}
    </div>
  );
};
```

### Common Pitfall: `&&` with Numbers

```jsx
// BUG — renders "0" when count is 0 (0 is falsy but renders in JSX)
{count && <Items />}

// CORRECT — use explicit boolean
{count > 0 && <Items />}
{Boolean(count) && <Items />}
{!!count && <Items />}
```

---

## 8. Lists & Keys

### Rendering Lists

```jsx
const products = [
  { id: 1, name: 'Laptop', price: 999 },
  { id: 2, name: 'Phone', price: 599 },
  { id: 3, name: 'Tablet', price: 399 },
];

const ProductList = () => (
  <ul>
    {products.map(product => (
      <li key={product.id}>
        {product.name} — ${product.price}
      </li>
    ))}
  </ul>
);
```

### Keys — Why They Matter

Keys help React identify which items changed, were added, or removed. They must be unique among siblings.

```jsx
// BAD — using index as key (causes bugs with reordering)
{items.map((item, index) => <Item key={index} item={item} />)}

// GOOD — using stable unique ID
{items.map(item => <Item key={item.id} item={item} />)}
```

**Why index keys cause bugs:**
```
Initial: [A(key=0), B(key=1), C(key=2)]
Remove A: [B(key=0), C(key=1)]

React thinks key=0 is still A — it reuses A's component instance for B.
Input fields, animations, and component state get mismatched.
```

### Interview Q&A
**Q: Can I use index as a key in a list?**
> Only when the list is static (never reordered, filtered, or items added/removed). For dynamic lists, always use a stable unique identifier. Using index as key with dynamic lists leads to bugs with component state, form inputs, and animations.

---

## 9. Forms & Controlled Components

### Controlled Components
React controls the form input's value via state. The state is the single source of truth.

```jsx
const LoginForm = () => {
  const [formData, setFormData] = useState({
    email: '',
    password: '',
    rememberMe: false
  });
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
  };

  const validate = () => {
    const newErrors = {};
    if (!formData.email.includes('@')) newErrors.email = 'Invalid email';
    if (formData.password.length < 6) newErrors.password = 'Min 6 characters';
    return newErrors;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const validationErrors = validate();
    if (Object.keys(validationErrors).length > 0) {
      setErrors(validationErrors);
      return;
    }
    // Submit form
    console.log(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      {errors.email && <span className="error">{errors.email}</span>}

      <input
        type="password"
        name="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Password"
      />
      {errors.password && <span className="error">{errors.password}</span>}

      <label>
        <input
          type="checkbox"
          name="rememberMe"
          checked={formData.rememberMe}
          onChange={handleChange}
        />
        Remember Me
      </label>

      <button type="submit">Login</button>
    </form>
  );
};
```

### Uncontrolled Components (useRef)

The DOM itself manages the form data. React reads values via refs.

```jsx
const UncontrolledForm = () => {
  const emailRef = useRef();
  const passwordRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(emailRef.current.value);
    console.log(passwordRef.current.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={emailRef} type="email" defaultValue="" />
      <input ref={passwordRef} type="password" />
      <button type="submit">Submit</button>
    </form>
  );
};
```

### React Hook Form (Popular Library)

```jsx
import { useForm } from 'react-hook-form';

const LoginForm = () => {
  const { register, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register('email', {
          required: 'Email is required',
          pattern: { value: /\S+@\S+\.\S+/, message: 'Invalid email' }
        })}
      />
      {errors.email && <p>{errors.email.message}</p>}

      <input
        type="password"
        {...register('password', { required: true, minLength: 6 })}
      />

      <button type="submit">Login</button>
    </form>
  );
};
```

---

## 10. Component Lifecycle

### Functional Component Lifecycle with useEffect

```jsx
useEffect(() => {
  // componentDidMount — runs once after first render
}, []);

useEffect(() => {
  // componentDidUpdate — runs when 'value' changes
}, [value]);

useEffect(() => {
  // Runs on every render (no dependency array)
});

useEffect(() => {
  const subscription = subscribe();
  return () => {
    subscription.unsubscribe(); // componentWillUnmount — cleanup
  };
}, []);
```

### Class Component Lifecycle Methods

```jsx
class MyComponent extends Component {
  // 1. MOUNTING PHASE
  constructor(props) {
    super(props);
    this.state = { data: null };
    // Initialize state, bind methods
  }

  static getDerivedStateFromProps(props, state) {
    // Sync state to props before render
    return null;
  }

  componentDidMount() {
    // After first render: fetch data, subscribe, set timers
    this.fetchData();
  }

  // 2. UPDATING PHASE
  shouldComponentUpdate(nextProps, nextState) {
    // Performance optimization: return false to skip re-render
    return nextProps.id !== this.props.id;
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // Capture DOM info before update (e.g., scroll position)
    return this.listRef.scrollHeight;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // After update: compare prev vs current props/state
    if (prevProps.userId !== this.props.userId) {
      this.fetchData();
    }
  }

  // 3. UNMOUNTING PHASE
  componentWillUnmount() {
    // Cleanup: clear timers, cancel requests, unsubscribe
    clearInterval(this.timer);
  }

  render() {
    return <div>{this.state.data}</div>;
  }
}
```

### Lifecycle Comparison

| Class Method | Hook Equivalent |
|-------------|----------------|
| componentDidMount | useEffect(() => {}, []) |
| componentDidUpdate | useEffect(() => {}, [dep]) |
| componentWillUnmount | useEffect cleanup function |
| shouldComponentUpdate | React.memo / useMemo |
| getDerivedStateFromProps | useState + useEffect pattern |

---

## 11. React Hooks — Core

### useState

```jsx
const [state, setState] = useState(initialValue);

// Lazy initialization (for expensive calculations)
const [state, setState] = useState(() => computeExpensiveValue());

// Primitive
const [count, setCount] = useState(0);

// Object
const [user, setUser] = useState({ name: '', email: '' });

// Array
const [items, setItems] = useState([]);

// Boolean toggle
const [isOpen, setIsOpen] = useState(false);
const toggle = () => setIsOpen(prev => !prev);
```

### useEffect

```jsx
import { useEffect, useState } from 'react';

const UserProfile = ({ userId }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let isMounted = true; // Prevent state update on unmounted component

    const fetchUser = async () => {
      try {
        setLoading(true);
        const res = await fetch(`/api/users/${userId}`);
        const data = await res.json();
        if (isMounted) {
          setUser(data);
          setError(null);
        }
      } catch (err) {
        if (isMounted) setError(err.message);
      } finally {
        if (isMounted) setLoading(false);
      }
    };

    fetchUser();

    return () => { isMounted = false; }; // Cleanup
  }, [userId]); // Re-run when userId changes

  if (loading) return <Spinner />;
  if (error) return <Error message={error} />;
  return <div>{user?.name}</div>;
};
```

### useContext

```jsx
import { createContext, useContext, useState } from 'react';

// Create context
const ThemeContext = createContext('light');

// Provider (wrap components that need the context)
const App = () => {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Dashboard />
    </ThemeContext.Provider>
  );
};

// Consumer (any nested component)
const ThemeToggle = () => {
  const { theme, setTheme } = useContext(ThemeContext);

  return (
    <button onClick={() => setTheme(t => t === 'light' ? 'dark' : 'light')}>
      Current: {theme}
    </button>
  );
};
```

### useRef

```jsx
import { useRef, useEffect } from 'react';

const InputFocus = () => {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus(); // Access DOM element directly
  }, []);

  return <input ref={inputRef} type="text" />;
};

// Storing mutable value without re-render
const Timer = () => {
  const timerRef = useRef(null);
  const [count, setCount] = useState(0);

  const start = () => {
    timerRef.current = setInterval(() => {
      setCount(prev => prev + 1);
    }, 1000);
  };

  const stop = () => clearInterval(timerRef.current);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </div>
  );
};
```

### Interview Q&A
**Q: What is the Rules of Hooks?**
> 1. Only call hooks at the top level (not inside loops, conditions, or nested functions). 2. Only call hooks from React function components or custom hooks. This ensures hook call order is consistent across renders.

**Q: Why does useEffect run twice in development mode (React 18)?**
> React 18 Strict Mode intentionally mounts components twice to help detect side effects that aren't properly cleaned up. This only happens in development — not in production.

---

## 12. React Hooks — Advanced

### useReducer

```jsx
import { useReducer } from 'react';

const initialState = { count: 0, step: 1 };

const reducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT': return { ...state, count: state.count + state.step };
    case 'DECREMENT': return { ...state, count: state.count - state.step };
    case 'RESET':     return initialState;
    case 'SET_STEP':  return { ...state, step: action.payload };
    default: throw new Error(`Unknown action: ${action.type}`);
  }
};

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-</button>
      <button onClick={() => dispatch({ type: 'RESET' })}>Reset</button>
      <input
        type="number"
        value={state.step}
        onChange={e => dispatch({ type: 'SET_STEP', payload: +e.target.value })}
      />
    </div>
  );
};
```

### useMemo

Memoizes the result of an expensive calculation.

```jsx
import { useMemo, useState } from 'react';

const ProductList = ({ products, filterText }) => {
  // Only recomputes when products or filterText changes
  const filteredProducts = useMemo(() => {
    console.log('Filtering...'); // Expensive operation
    return products
      .filter(p => p.name.toLowerCase().includes(filterText.toLowerCase()))
      .sort((a, b) => a.price - b.price);
  }, [products, filterText]);

  return (
    <ul>
      {filteredProducts.map(p => <li key={p.id}>{p.name}</li>)}
    </ul>
  );
};
```

### useCallback

Memoizes a function reference.

```jsx
import { useCallback, useState } from 'react';

const Parent = () => {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([]);

  // Without useCallback: new function reference on every render
  // → Child re-renders even when items haven't changed

  // With useCallback: stable function reference when deps don't change
  const handleAddItem = useCallback((item) => {
    setItems(prev => [...prev, item]);
  }, []); // empty deps — function never changes

  return (
    <>
      <button onClick={() => setCount(c => c + 1)}>Count: {count}</button>
      <ChildComponent onAdd={handleAddItem} />
    </>
  );
};

const ChildComponent = React.memo(({ onAdd }) => {
  console.log('Child rendered');
  return <button onClick={() => onAdd('item')}>Add Item</button>;
});
```

### useLayoutEffect

Like `useEffect` but fires synchronously **before** the browser paints.

```jsx
import { useLayoutEffect, useRef, useState } from 'react';

const Tooltip = ({ text, targetRef }) => {
  const tooltipRef = useRef();
  const [position, setPosition] = useState({ top: 0, left: 0 });

  // Use useLayoutEffect to measure DOM and position tooltip
  // BEFORE the browser paints (prevents flicker)
  useLayoutEffect(() => {
    const targetRect = targetRef.current.getBoundingClientRect();
    const tooltipRect = tooltipRef.current.getBoundingClientRect();
    setPosition({
      top: targetRect.top - tooltipRect.height,
      left: targetRect.left
    });
  }, [text]);

  return (
    <div ref={tooltipRef} style={{ position: 'fixed', ...position }}>
      {text}
    </div>
  );
};
```

### useImperativeHandle

Customizes what a parent can access via a ref to a child component.

```jsx
import { forwardRef, useImperativeHandle, useRef } from 'react';

const CustomInput = forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
    clear: () => { inputRef.current.value = ''; },
    getValue: () => inputRef.current.value
  }));

  return <input ref={inputRef} {...props} />;
});

// Parent
const Form = () => {
  const inputRef = useRef();

  return (
    <>
      <CustomInput ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>Focus</button>
      <button onClick={() => inputRef.current.clear()}>Clear</button>
    </>
  );
};
```

---

## 13. Context API

### When to Use Context
Context is designed for **global data** that many components at different nesting levels need access to: theme, locale, authentication, current user.

### Complete Context Pattern

```jsx
import { createContext, useContext, useState, useCallback } from 'react';

// 1. Create context with default value
const AuthContext = createContext(null);

// 2. Create Provider component
export const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);

  const login = useCallback(async (credentials) => {
    setLoading(true);
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        body: JSON.stringify(credentials)
      });
      const userData = await response.json();
      setUser(userData);
    } finally {
      setLoading(false);
    }
  }, []);

  const logout = useCallback(() => {
    setUser(null);
    localStorage.removeItem('token');
  }, []);

  return (
    <AuthContext.Provider value={{ user, loading, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

// 3. Custom hook for consuming context (best practice)
export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
};

// 4. Wrap app
const App = () => (
  <AuthProvider>
    <Router />
  </AuthProvider>
);

// 5. Use anywhere in the tree
const Navbar = () => {
  const { user, logout } = useAuth();

  return (
    <nav>
      {user ? (
        <>
          <span>Hello, {user.name}</span>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <Link to="/login">Login</Link>
      )}
    </nav>
  );
};
```

### Context Performance — Avoiding Re-renders

Every consumer re-renders when the context value changes. Split contexts by update frequency:

```jsx
// BAD — all consumers re-render when anything changes
const AppContext = createContext({ user, theme, notifications, settings });

// GOOD — split into separate contexts
const UserContext = createContext(null);     // Changes on login/logout
const ThemeContext = createContext('light'); // Changes on theme toggle
const NotifContext = createContext([]);      // Changes frequently
```

### Interview Q&A
**Q: When would you use Context vs Redux?**
> Context is suitable for low-frequency global data (auth, theme, locale). Redux is better for complex state with frequent updates, middleware needs (logging, async), time-travel debugging, or when many components across the app read and write the same state.

---

## 14. useReducer & State Management

### useReducer vs useState

| Aspect | useState | useReducer |
|--------|---------|-----------|
| Best for | Simple, independent state | Complex state logic |
| State shape | Primitive or simple object | Complex objects |
| Update logic | Inline in handlers | Centralized in reducer |
| Testability | Harder | Easy (pure function) |
| When to use | 1-3 state variables | 3+ related state variables |

### Complex State Management Example

```jsx
const cartReducer = (state, action) => {
  switch (action.type) {
    case 'ADD_ITEM': {
      const existing = state.items.find(i => i.id === action.payload.id);
      if (existing) {
        return {
          ...state,
          items: state.items.map(i =>
            i.id === action.payload.id
              ? { ...i, quantity: i.quantity + 1 }
              : i
          )
        };
      }
      return {
        ...state,
        items: [...state.items, { ...action.payload, quantity: 1 }]
      };
    }
    case 'REMOVE_ITEM':
      return {
        ...state,
        items: state.items.filter(i => i.id !== action.payload)
      };
    case 'CLEAR_CART':
      return { ...state, items: [] };
    default:
      return state;
  }
};

const Cart = () => {
  const [cart, dispatch] = useReducer(cartReducer, { items: [] });

  const total = cart.items.reduce((sum, item) => sum + item.price * item.quantity, 0);

  return (
    <div>
      {cart.items.map(item => (
        <div key={item.id}>
          {item.name} × {item.quantity}
          <button onClick={() => dispatch({ type: 'REMOVE_ITEM', payload: item.id })}>
            Remove
          </button>
        </div>
      ))}
      <p>Total: ${total.toFixed(2)}</p>
      <button onClick={() => dispatch({ type: 'CLEAR_CART' })}>Clear</button>
    </div>
  );
};
```

---

## 15. React Router

### Setup (React Router v6)

```bash
npm install react-router-dom
```

```jsx
import { BrowserRouter, Routes, Route, Link, NavLink,
         useNavigate, useParams, useLocation, useSearchParams,
         Navigate, Outlet } from 'react-router-dom';

const App = () => (
  <BrowserRouter>
    <Navbar />
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="/users" element={<UserList />} />
      <Route path="/users/:id" element={<UserDetail />} />

      {/* Nested routes */}
      <Route path="/dashboard" element={<DashboardLayout />}>
        <Route index element={<DashboardHome />} />
        <Route path="profile" element={<Profile />} />
        <Route path="settings" element={<Settings />} />
      </Route>

      {/* Protected route */}
      <Route path="/admin" element={<ProtectedRoute><Admin /></ProtectedRoute>} />

      {/* 404 */}
      <Route path="*" element={<NotFound />} />
    </Routes>
  </BrowserRouter>
);
```

### Navigation

```jsx
// Link (no page reload)
<Link to="/about">About</Link>

// NavLink (adds 'active' class when route matches)
<NavLink to="/users" className={({ isActive }) => isActive ? 'active' : ''}>
  Users
</NavLink>

// Programmatic navigation
const navigate = useNavigate();
navigate('/dashboard');
navigate(-1);              // Go back
navigate('/login', { state: { from: '/admin' } }); // With state
navigate('/search', { replace: true }); // Replace history entry
```

### Route Params & Query Strings

```jsx
// Route: /users/:id/posts/:postId
const UserPost = () => {
  const { id, postId } = useParams();
  const [searchParams, setSearchParams] = useSearchParams();
  const location = useLocation();

  const page = searchParams.get('page') || 1;
  const sort = searchParams.get('sort') || 'newest';

  return (
    <div>
      <p>User: {id}, Post: {postId}</p>
      <p>Page: {page}, Sort: {sort}</p>
      <p>Full path: {location.pathname}</p>
      <button onClick={() => setSearchParams({ page: +page + 1, sort })}>
        Next Page
      </button>
    </div>
  );
};
```

### Protected Routes

```jsx
const ProtectedRoute = ({ children }) => {
  const { user } = useAuth();
  const location = useLocation();

  if (!user) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return children;
};
```

### Nested Routes with Outlet

```jsx
const DashboardLayout = () => (
  <div className="dashboard">
    <Sidebar />
    <main>
      <Outlet /> {/* Renders matched child route */}
    </main>
  </div>
);
```

---

## 16. Code Splitting & Lazy Loading

### React.lazy & Suspense

```jsx
import { lazy, Suspense } from 'react';

// Lazy load components (splits into separate bundle chunks)
const Dashboard = lazy(() => import('./pages/Dashboard'));
const AdminPanel = lazy(() => import('./pages/AdminPanel'));
const Settings = lazy(() => import('./pages/Settings'));

const App = () => (
  <Suspense fallback={<LoadingSpinner />}>
    <Routes>
      <Route path="/dashboard" element={<Dashboard />} />
      <Route path="/admin" element={<AdminPanel />} />
      <Route path="/settings" element={<Settings />} />
    </Routes>
  </Suspense>
);
```

### Route-Based Code Splitting

The most effective strategy: each route loads its own bundle only when visited.

```jsx
// Before: all code loaded upfront (large initial bundle)
import Home from './pages/Home';
import Products from './pages/Products';
import Checkout from './pages/Checkout';

// After: each page loaded on demand (fast initial load)
const Home = lazy(() => import('./pages/Home'));
const Products = lazy(() => import('./pages/Products'));
const Checkout = lazy(() => import('./pages/Checkout'));
```

### Interview Q&A
**Q: What is the difference between `React.lazy` and dynamic `import()`?**
> `import()` is a native JavaScript feature that returns a Promise with the module. `React.lazy` wraps `import()` to work with React's rendering system and must be used with `Suspense` to show a fallback while loading.

---

## 17. Error Boundaries

### What are Error Boundaries?
Class components that catch JavaScript errors in their child component tree and display a fallback UI instead of crashing the whole app.

```jsx
class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    // Update state to trigger fallback UI
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    // Log to error reporting service
    console.error('Error:', error);
    console.error('Component stack:', errorInfo.componentStack);
    // logErrorToSentry(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h2>Something went wrong.</h2>
          <button onClick={() => this.setState({ hasError: false, error: null })}>
            Try Again
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <Route path="/dashboard" element={<Dashboard />} />
</ErrorBoundary>
```

### Error Boundaries Do NOT Catch:
- Event handler errors (use try/catch)
- Async errors (promises, setTimeout)
- Server-side rendering errors
- Errors in the error boundary itself

### Interview Q&A
**Q: Why can't functional components be error boundaries?**
> Error boundaries require `getDerivedStateFromError` and `componentDidCatch` lifecycle methods, which are only available in class components. However, libraries like `react-error-boundary` provide a functional wrapper.

---

## 18. Refs & the DOM

### useRef — Three Main Uses

**1. Access DOM elements:**
```jsx
const TextInput = () => {
  const ref = useRef(null);

  return (
    <>
      <input ref={ref} />
      <button onClick={() => ref.current.focus()}>Focus</button>
      <button onClick={() => console.log(ref.current.value)}>Get Value</button>
    </>
  );
};
```

**2. Store mutable value without triggering re-render:**
```jsx
const RenderCounter = () => {
  const renderCount = useRef(0);
  const [count, setCount] = useState(0);

  renderCount.current++;  // Doesn't cause re-render (unlike state)

  return (
    <div>
      <p>Value: {count}</p>
      <p>Renders: {renderCount.current}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
    </div>
  );
};
```

**3. Store previous value:**
```jsx
const usePrevious = (value) => {
  const prevRef = useRef();
  useEffect(() => {
    prevRef.current = value;
  }, [value]);
  return prevRef.current;
};

const Component = ({ count }) => {
  const prevCount = usePrevious(count);
  return <p>Current: {count}, Previous: {prevCount}</p>;
};
```

### forwardRef

Pass a ref through a component to a DOM element inside it.

```jsx
const Input = forwardRef(({ label, ...props }, ref) => (
  <div>
    <label>{label}</label>
    <input ref={ref} {...props} />
  </div>
));

// Parent
const Form = () => {
  const inputRef = useRef();

  return (
    <>
      <Input ref={inputRef} label="Email" type="email" />
      <button onClick={() => inputRef.current.focus()}>Focus Email</button>
    </>
  );
};
```

---

## 19. Higher-Order Components (HOC)

### What is an HOC?
A function that takes a component and returns a new enhanced component.

```jsx
// HOC pattern: withAuth
const withAuth = (WrappedComponent) => {
  return function AuthenticatedComponent(props) {
    const { user } = useAuth();

    if (!user) {
      return <Navigate to="/login" />;
    }

    return <WrappedComponent {...props} user={user} />;
  };
};

// Usage
const ProtectedDashboard = withAuth(Dashboard);
const ProtectedProfile = withAuth(Profile);
```

### HOC for Loading State

```jsx
const withLoading = (WrappedComponent) => {
  return function WithLoadingComponent({ isLoading, ...props }) {
    if (isLoading) return <Spinner />;
    return <WrappedComponent {...props} />;
  };
};

const UserListWithLoading = withLoading(UserList);

<UserListWithLoading isLoading={loading} users={users} />
```

### HOC Conventions
- Name HOC with `with` prefix (`withAuth`, `withLoading`)
- Pass through all props to wrapped component (`{...props}`)
- Set `displayName` for debugging: `WrappedComponent.displayName = 'withAuth(${name})'`

### Interview Q&A
**Q: What are the drawbacks of HOCs?**
> Prop collision (HOC and wrapped component may have same prop names), wrapper hell (many nested HOCs in DevTools), and non-obvious data source. Custom hooks solve most HOC use cases more elegantly.

---

## 20. Render Props Pattern

### What is Render Props?
Sharing code between components by passing a **function as a prop** that returns JSX.

```jsx
// DataFetcher — handles fetching logic
const DataFetcher = ({ url, render }) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(r => r.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return render({ data, loading, error });
};

// Usage — consumer decides how to render the data
<DataFetcher
  url="/api/users"
  render={({ data, loading, error }) => {
    if (loading) return <Spinner />;
    if (error) return <Error message={error.message} />;
    return <UserList users={data} />;
  }}
/>

// Children as function (alternative syntax)
<DataFetcher url="/api/users">
  {({ data, loading }) => loading ? <Spinner /> : <UserList users={data} />}
</DataFetcher>
```

### Interview Q&A
**Q: HOC vs Render Props vs Custom Hooks — when to use which?**
> Custom Hooks (modern React) are preferred for sharing logic between components. HOCs are good for wrapping components with additional behavior (auth, analytics). Render Props are useful when you need fine control over rendering. Custom Hooks have replaced most use cases for the other two patterns.

---

## 21. Custom Hooks

### What are Custom Hooks?
Functions starting with `use` that encapsulate and reuse stateful logic between components.

```jsx
// useFetch — reusable data fetching
const useFetch = (url, options = {}) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    const fetchData = async () => {
      try {
        setLoading(true);
        const res = await fetch(url, {
          ...options,
          signal: controller.signal
        });
        if (!res.ok) throw new Error(`HTTP error! Status: ${res.status}`);
        const result = await res.json();
        setData(result);
        setError(null);
      } catch (err) {
        if (err.name !== 'AbortError') setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
    return () => controller.abort(); // Cancel on unmount or url change
  }, [url]);

  return { data, loading, error };
};

// Usage
const UserList = () => {
  const { data: users, loading, error } = useFetch('/api/users');

  if (loading) return <Spinner />;
  if (error) return <Error message={error} />;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
};
```

### useLocalStorage

```jsx
const useLocalStorage = (key, initialValue) => {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored ? JSON.parse(stored) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setStoredValue = useCallback((newValue) => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  }, [key]);

  return [value, setStoredValue];
};

// Usage
const Settings = () => {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  return (
    <button onClick={() => setTheme(t => t === 'light' ? 'dark' : 'light')}>
      Theme: {theme}
    </button>
  );
};
```

### useDebounce

```jsx
const useDebounce = (value, delay = 300) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
};

// Usage — search as user types, debounced
const SearchBox = () => {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 500);

  const { data: results } = useFetch(
    debouncedQuery ? `/api/search?q=${debouncedQuery}` : null
  );

  return (
    <>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <SearchResults results={results} />
    </>
  );
};
```

### useWindowSize

```jsx
const useWindowSize = () => {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    const handleResize = () => setSize({
      width: window.innerWidth,
      height: window.innerHeight
    });

    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return size;
};
```

---

## 22. Performance Optimization

### Identifying Performance Problems
Use React DevTools Profiler to measure render times and find unnecessary re-renders.

### Causes of Unnecessary Re-renders
1. Parent re-renders (child always re-renders unless memoized)
2. Context value change (all consumers re-render)
3. New object/array/function reference on every render

### Optimization Techniques

**1. React.memo — Skip re-render if props unchanged:**
```jsx
const ExpensiveChild = React.memo(({ data, onSelect }) => {
  console.log('Child rendered');
  return <div onClick={() => onSelect(data.id)}>{data.name}</div>;
});
```

**2. useMemo — Memoize computed values:**
```jsx
const sortedList = useMemo(() =>
  [...data].sort((a, b) => a.name.localeCompare(b.name)),
  [data]
);
```

**3. useCallback — Stable function references:**
```jsx
const handleSelect = useCallback((id) => {
  setSelected(id);
}, []);
```

**4. Virtualization — Render only visible items:**
```jsx
import { FixedSizeList } from 'react-window';

const VirtualList = ({ items }) => (
  <FixedSizeList
    height={600}
    itemCount={items.length}
    itemSize={50}
    width="100%"
  >
    {({ index, style }) => (
      <div style={style}>{items[index].name}</div>
    )}
  </FixedSizeList>
);
```

**5. Debounce expensive operations:**
```jsx
const handleSearch = useCallback(
  debounce((query) => fetchResults(query), 300),
  []
);
```

**6. Avoid inline objects/functions in JSX:**
```jsx
// BAD — new object reference on every render
<Component style={{ color: 'red' }} onClick={() => handleClick(id)} />

// GOOD — stable references
const style = useMemo(() => ({ color: 'red' }), []);
const handleClick = useCallback(() => doSomething(id), [id]);
<Component style={style} onClick={handleClick} />
```

### Interview Q&A
**Q: When should you use React.memo?**
> Use it when a component re-renders frequently with the same props, and re-rendering is expensive. Don't use it by default everywhere — it has a cost (comparison on every render) and can be counter-productive if props change frequently.

---

## 23. React.memo, useMemo & useCallback

### React.memo — Component Memoization

```jsx
// Only re-renders if props change (shallow comparison by default)
const UserCard = React.memo(({ user, onDelete }) => {
  return (
    <div>
      <h3>{user.name}</h3>
      <button onClick={() => onDelete(user.id)}>Delete</button>
    </div>
  );
});

// Custom comparison function
const UserCard = React.memo(({ user }) => {
  return <div>{user.name}</div>;
}, (prevProps, nextProps) => {
  // Return true to skip re-render, false to re-render
  return prevProps.user.id === nextProps.user.id &&
         prevProps.user.name === nextProps.user.name;
});
```

### useMemo vs useCallback Comparison

```jsx
// useMemo — returns a memoized VALUE
const total = useMemo(() => {
  return items.reduce((sum, item) => sum + item.price, 0);
}, [items]);
// total is a number

// useCallback — returns a memoized FUNCTION
const handleAdd = useCallback((item) => {
  setItems(prev => [...prev, item]);
}, []);
// handleAdd is a function

// useMemo can replicate useCallback:
const handleAdd = useMemo(() => (item) => {
  setItems(prev => [...prev, item]);
}, []);
// Equivalent but useCallback is cleaner
```

### When NOT to Memoize

```jsx
// DON'T memoize simple calculations
const double = useMemo(() => count * 2, [count]); // Overkill

// DON'T memoize components that always receive new props
const Input = React.memo(({ value, onChange }) => ...); // Pointless if value changes on every keystroke

// DON'T memoize everything by default — profile first
```

---

## 24. Virtual DOM & Reconciliation

### What is the Virtual DOM?
A lightweight JavaScript representation of the actual DOM, kept in memory. When state changes, React computes the difference (diff) and updates only the changed parts of the real DOM.

```
State Change → New VDOM Tree → Diff with Previous VDOM → Update Real DOM (minimal changes)
```

### Reconciliation Algorithm (React Diffing)

React uses heuristics to make diffing O(n) instead of O(n³):

**Rule 1: Elements of different types rebuild the tree:**
```jsx
// React destroys old tree and builds new one
// Before: <div>...</div>
// After:  <span>...</span> → Full rebuild
```

**Rule 2: Same element type, update attributes:**
```jsx
// React keeps the DOM node, only updates changed attributes
// Before: <div className="old" title="A" />
// After:  <div className="new" title="A" />
// Only className is updated
```

**Rule 3: Keys help identify list items:**
```jsx
// Without keys: React re-renders entire list on change
// With keys: React matches items by key and only updates changed ones
```

### React Fiber

React 16 introduced **Fiber**, a complete rewrite of the reconciliation engine.

**Key improvements:**
- **Incremental rendering:** Split rendering work into chunks
- **Prioritization:** High-priority updates (user input) interrupt low-priority ones (data fetches)
- **Concurrency:** Multiple UI states can be prepared simultaneously
- **Suspense & Concurrent Mode** built on Fiber

### Interview Q&A
**Q: What is the difference between the Virtual DOM and the Shadow DOM?**
> Virtual DOM is React's in-memory representation of the UI, used to efficiently batch DOM updates. Shadow DOM is a browser-native feature for encapsulating styles and markup in Web Components. They are completely different concepts.

---

## 25. State Management — Redux

### Core Concepts

```
Action → Reducer → Store → View (React) → Action (cycle repeats)
```

- **Store:** Single source of truth — holds entire app state
- **Action:** Plain object describing what happened `{ type: 'INCREMENT', payload: 1 }`
- **Reducer:** Pure function `(state, action) => newState`
- **Dispatch:** Send an action to the store
- **Selector:** Extract specific data from the store

### Redux Data Flow

```
User interaction
    ↓
dispatch(action)
    ↓
Reducer(currentState, action) → newState
    ↓
Store updated
    ↓
Connected components re-render
```

### Classic Redux (Verbose — for understanding)

```jsx
// actions.js
export const increment = (amount) => ({ type: 'INCREMENT', payload: amount });
export const decrement = (amount) => ({ type: 'DECREMENT', payload: amount });

// reducer.js
const initialState = { count: 0 };

const counterReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'INCREMENT': return { count: state.count + action.payload };
    case 'DECREMENT': return { count: state.count - action.payload };
    default: return state;
  }
};

// store.js
import { createStore } from 'redux';
const store = createStore(counterReducer);

// Component
import { useSelector, useDispatch } from 'react-redux';

const Counter = () => {
  const count = useSelector(state => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => dispatch(increment(1))}>+</button>
    </div>
  );
};
```

---

## 26. Redux Toolkit (RTK)

### Why RTK?
Redux Toolkit is the **official, opinionated way** to write Redux. It eliminates boilerplate using `createSlice` and `configureStore`.

```bash
npm install @reduxjs/toolkit react-redux
```

### createSlice

```jsx
// features/counter/counterSlice.js
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0, step: 1 },
  reducers: {
    increment: (state) => { state.value += state.step; }, // Immer allows "mutation"
    decrement: (state) => { state.value -= state.step; },
    reset:     (state) => { state.value = 0; },
    setStep:   (state, action) => { state.step = action.payload; },
    addByAmount: (state, action) => { state.value += action.payload; }
  }
});

export const { increment, decrement, reset, setStep, addByAmount } = counterSlice.actions;
export default counterSlice.reducer;
```

### configureStore

```jsx
// app/store.js
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from '../features/counter/counterSlice';
import authReducer from '../features/auth/authSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    auth: authReducer
  }
  // Redux DevTools and thunk middleware included by default
});

// main.jsx
import { Provider } from 'react-redux';

ReactDOM.createRoot(document.getElementById('root')).render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

### RTK Query (Data Fetching)

```jsx
// services/api.js
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const api = createApi({
  reducerPath: 'api',
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
  tagTypes: ['User'],
  endpoints: (builder) => ({
    getUsers: builder.query({
      query: () => '/users',
      providesTags: ['User']
    }),
    getUserById: builder.query({
      query: (id) => `/users/${id}`
    }),
    createUser: builder.mutation({
      query: (body) => ({ url: '/users', method: 'POST', body }),
      invalidatesTags: ['User'] // Auto-refetch users after creating
    }),
    deleteUser: builder.mutation({
      query: (id) => ({ url: `/users/${id}`, method: 'DELETE' }),
      invalidatesTags: ['User']
    })
  })
});

export const {
  useGetUsersQuery,
  useGetUserByIdQuery,
  useCreateUserMutation,
  useDeleteUserMutation
} = api;

// Usage in component
const UserList = () => {
  const { data: users, isLoading, isError } = useGetUsersQuery();
  const [deleteUser] = useDeleteUserMutation();

  if (isLoading) return <Spinner />;
  if (isError) return <Error />;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
          {user.name}
          <button onClick={() => deleteUser(user.id)}>Delete</button>
        </li>
      ))}
    </ul>
  );
};
```

---

## 27. React Query (TanStack Query)

### Why React Query?
Manages server state (fetching, caching, synchronizing, updating data from server) with minimal code.

```bash
npm install @tanstack/react-query
```

```jsx
// main.jsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000, // 5 minutes
      retry: 2
    }
  }
});

<QueryClientProvider client={queryClient}>
  <App />
</QueryClientProvider>
```

### useQuery

```jsx
import { useQuery } from '@tanstack/react-query';

const fetchUser = (id) => fetch(`/api/users/${id}`).then(r => r.json());

const UserProfile = ({ userId }) => {
  const {
    data: user,
    isLoading,
    isError,
    error,
    isFetching,    // True when background refetch is happening
    refetch        // Manual refetch trigger
  } = useQuery({
    queryKey: ['user', userId],     // Unique cache key
    queryFn: () => fetchUser(userId),
    staleTime: 60 * 1000,          // Consider fresh for 1 min
    enabled: !!userId,              // Only fetch when userId exists
    select: (data) => data.profile  // Transform/select data
  });

  if (isLoading) return <Spinner />;
  if (isError) return <Error message={error.message} />;

  return (
    <div>
      {isFetching && <small>Refreshing...</small>}
      <h1>{user.name}</h1>
      <button onClick={refetch}>Refresh</button>
    </div>
  );
};
```

### useMutation

```jsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

const CreateUser = () => {
  const queryClient = useQueryClient();

  const { mutate: createUser, isPending } = useMutation({
    mutationFn: (userData) =>
      fetch('/api/users', {
        method: 'POST',
        body: JSON.stringify(userData)
      }).then(r => r.json()),
    onSuccess: (newUser) => {
      // Invalidate and refetch
      queryClient.invalidateQueries({ queryKey: ['users'] });
      // OR optimistically update the cache
      queryClient.setQueryData(['users'], old => [...old, newUser]);
    },
    onError: (error) => {
      console.error('Failed:', error);
    }
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    createUser({ name: 'Alice', email: 'alice@example.com' });
  };

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit" disabled={isPending}>
        {isPending ? 'Creating...' : 'Create User'}
      </button>
    </form>
  );
};
```

---

## 28. Testing in React

### Testing Pyramid

```
         /\
        /E2E\           (Cypress, Playwright) — Slow, few tests
       /------\
      /Integration\     (RTL + MSW) — Key user flows
     /------------\
    /  Unit Tests  \    (Jest + RTL) — Fast, many tests
   /--------------\
```

### React Testing Library (RTL) Philosophy
Test behavior, not implementation. Query elements the way users interact with them.

```jsx
// Component to test
const Counter = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p data-testid="count">Count: {count}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
      <button onClick={() => setCount(c => c - 1)}>Decrement</button>
    </div>
  );
};

// Test
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

describe('Counter', () => {
  it('renders initial count of 0', () => {
    render(<Counter />);
    expect(screen.getByTestId('count')).toHaveTextContent('Count: 0');
  });

  it('increments count when button clicked', async () => {
    render(<Counter />);
    await userEvent.click(screen.getByRole('button', { name: /increment/i }));
    expect(screen.getByTestId('count')).toHaveTextContent('Count: 1');
  });
});
```

### Testing Async Components

```jsx
// Mock API
jest.mock('./api', () => ({
  fetchUsers: jest.fn()
}));

import { fetchUsers } from './api';

it('loads and displays users', async () => {
  fetchUsers.mockResolvedValue([
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' }
  ]);

  render(<UserList />);

  expect(screen.getByText(/loading/i)).toBeInTheDocument();

  await waitFor(() => {
    expect(screen.getByText('Alice')).toBeInTheDocument();
    expect(screen.getByText('Bob')).toBeInTheDocument();
  });
});
```

### RTL Query Priority

```
getByRole          → Best (semantic, accessible)
getByLabelText     → Form inputs
getByPlaceholderText → Less preferred
getByText          → Non-interactive elements
getByDisplayValue  → Form elements with value
getByAltText       → Images
getByTitle         → Title attribute
getByTestId        → Last resort (avoid if possible)
```

---

## 29. Styling in React

### CSS Modules

```jsx
// Button.module.css
.button {
  background: blue;
  color: white;
  padding: 8px 16px;
}

.primary { background: royalblue; }
.danger  { background: crimson; }

// Button.jsx
import styles from './Button.module.css';

const Button = ({ variant = 'primary', children }) => (
  <button className={`${styles.button} ${styles[variant]}`}>
    {children}
  </button>
);
```

### Styled Components

```jsx
import styled, { css } from 'styled-components';

const Button = styled.button`
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  background: ${({ variant }) => variant === 'danger' ? 'crimson' : 'royalblue'};
  color: white;

  ${({ size }) => size === 'large' && css`
    padding: 12px 24px;
    font-size: 18px;
  `}

  &:hover { opacity: 0.9; }
`;

// Usage
<Button variant="danger" size="large">Delete</Button>
```

### Tailwind CSS

```jsx
const Button = ({ children, variant = 'primary' }) => {
  const variants = {
    primary: 'bg-blue-500 hover:bg-blue-600',
    danger:  'bg-red-500 hover:bg-red-600',
    ghost:   'bg-transparent border border-gray-300 hover:bg-gray-100'
  };

  return (
    <button className={`px-4 py-2 rounded text-white font-medium ${variants[variant]}`}>
      {children}
    </button>
  );
};
```

---

## 30. Server-Side Rendering & Next.js

### CSR vs SSR vs SSG vs ISR

| Type | Description | When to Use |
|------|-------------|-------------|
| CSR (Client-Side Rendering) | Browser downloads JS, renders app | Dashboards, SPAs |
| SSR (Server-Side Rendering) | Server renders HTML per request | SEO-critical, dynamic content |
| SSG (Static Site Generation) | HTML generated at build time | Blogs, marketing pages |
| ISR (Incremental Static Regeneration) | Regenerate static pages in background | Product pages, news sites |

### Next.js Key Features

```jsx
// app/users/page.tsx — Server Component (Next.js 13+ App Router)
// Runs on server — no useEffect, no useState
async function UsersPage() {
  const users = await fetch('https://api.example.com/users').then(r => r.json());

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

// File-system routing
// app/page.tsx          → /
// app/about/page.tsx    → /about
// app/users/[id]/page.tsx → /users/123

// Dynamic route params
async function UserPage({ params }) {
  const user = await fetch(`/api/users/${params.id}`).then(r => r.json());
  return <UserProfile user={user} />;
}
```

### Interview Q&A
**Q: What is the difference between SSR and SSG?**
> SSR renders pages on the server for every request (fresh data, higher server load). SSG renders at build time and serves static HTML (faster, but data can be stale). ISR combines both — generates static HTML but revalidates in the background at a set interval.

---

## 31. React Patterns & Best Practices

### Compound Components

```jsx
// Components that share implicit state
const Select = ({ children, value, onChange }) => {
  return (
    <SelectContext.Provider value={{ value, onChange }}>
      <div className="select">{children}</div>
    </SelectContext.Provider>
  );
};

Select.Option = ({ value, children }) => {
  const { value: selected, onChange } = useContext(SelectContext);
  return (
    <div
      className={selected === value ? 'selected' : ''}
      onClick={() => onChange(value)}
    >
      {children}
    </div>
  );
};

// Usage
<Select value={selected} onChange={setSelected}>
  <Select.Option value="react">React</Select.Option>
  <Select.Option value="vue">Vue</Select.Option>
  <Select.Option value="angular">Angular</Select.Option>
</Select>
```

### Container/Presenter Pattern

```jsx
// Container — handles logic and data
const UserListContainer = () => {
  const { data: users, loading, error } = useFetch('/api/users');
  const [filter, setFilter] = useState('');

  const filteredUsers = users?.filter(u =>
    u.name.toLowerCase().includes(filter.toLowerCase())
  );

  return (
    <UserListPresenter
      users={filteredUsers}
      loading={loading}
      error={error}
      filter={filter}
      onFilterChange={setFilter}
    />
  );
};

// Presenter — handles only rendering (pure, easily testable)
const UserListPresenter = ({ users, loading, error, filter, onFilterChange }) => {
  if (loading) return <Spinner />;
  if (error) return <Error message={error} />;

  return (
    <div>
      <input value={filter} onChange={e => onFilterChange(e.target.value)} />
      {users.map(u => <UserCard key={u.id} user={u} />)}
    </div>
  );
};
```

### Best Practices Summary

```
✅ Keep components small and focused (single responsibility)
✅ Lift state to the nearest common ancestor
✅ Use custom hooks to share logic
✅ Prefer composition over inheritance
✅ Keep side effects in useEffect (or custom hooks)
✅ Use meaningful, descriptive names (isLoading not loading2)
✅ Avoid prop drilling beyond 2-3 levels → use Context
✅ Memoize only when profiling shows a problem
✅ Co-locate related files (component, test, styles together)
✅ Use TypeScript for large projects

❌ Don't mutate state directly
❌ Don't put everything in global state
❌ Don't use index as list key (for dynamic lists)
❌ Don't fetch data without cleanup (memory leaks)
❌ Don't over-engineer small apps with Redux
❌ Don't use && with numbers in JSX
```

---

## 32. React 18 — New Features

### Automatic Batching

```jsx
// React 17: Only batched inside React event handlers
// React 18: Batches ALL state updates (even in setTimeout, Promises, native events)

// Before (React 17): Two separate re-renders
setTimeout(() => {
  setCount(c => c + 1); // re-render
  setFlag(f => !f);     // re-render
}, 1000);

// After (React 18): One combined re-render
setTimeout(() => {
  setCount(c => c + 1); // batched
  setFlag(f => !f);     // batched → single re-render
}, 1000);

// Opt out of batching when needed:
import { flushSync } from 'react-dom';
flushSync(() => setCount(c => c + 1)); // Forces immediate re-render
```

### Concurrent Features

```jsx
// useTransition — mark state update as non-urgent
import { useTransition, useState } from 'react';

const SearchPage = () => {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleSearch = (e) => {
    const value = e.target.value;
    setQuery(value); // Urgent: update input immediately

    startTransition(() => {
      // Non-urgent: can be interrupted by more urgent updates
      setResults(searchDatabase(value));
    });
  };

  return (
    <>
      <input value={query} onChange={handleSearch} />
      {isPending ? <Spinner /> : <SearchResults results={results} />}
    </>
  );
};
```

### useDeferredValue

```jsx
import { useDeferredValue, memo } from 'react';

const SearchResults = memo(({ query }) => {
  // Expensive computation
  const results = searchDatabase(query);
  return <ResultList results={results} />;
});

const Search = () => {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query); // Lags behind query

  return (
    <>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      {/* Renders with old deferredQuery while new one computes */}
      <SearchResults query={deferredQuery} />
    </>
  );
};
```

### Suspense on Server (SSR)

React 18 enables streaming SSR with Suspense — server can send HTML chunks as they're ready, instead of waiting for everything.

```jsx
// Server streams HTML immediately for shell, then streams content as it resolves
<Suspense fallback={<Spinner />}>
  <SlowComponent />   {/* Server sends spinner HTML immediately */}
</Suspense>            {/* Then streams SlowComponent HTML when ready */}
```

### useId

```jsx
// Generate unique, stable IDs for accessibility (safe for SSR)
import { useId } from 'react';

const FormField = ({ label }) => {
  const id = useId(); // e.g., ":r1:"

  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input id={id} type="text" />
    </div>
  );
};
```

### Interview Q&A
**Q: What is Concurrent Mode in React 18?**
> Concurrent Mode allows React to prepare multiple UI states simultaneously and interrupt, pause, resume, or abandon renders. This enables features like `useTransition` (mark updates as non-urgent), `useDeferredValue` (defer expensive re-renders), and streaming SSR. It makes React apps feel more responsive to user input.

---

## Quick Reference Cheat Sheet

### Hooks at a Glance

| Hook | Purpose | When to Use |
|------|---------|-------------|
| useState | Local state | Simple state in a component |
| useEffect | Side effects | Data fetching, subscriptions, timers |
| useContext | Consume context | Global theme, auth, locale |
| useRef | DOM refs / mutable value | Focus, timers, previous values |
| useReducer | Complex state logic | Multiple related state variables |
| useMemo | Memoize value | Expensive calculations |
| useCallback | Memoize function | Stable callback for memoized children |
| useLayoutEffect | DOM measurement | Before paint, prevent flicker |
| useTransition | Non-urgent updates | Search, filtering large lists |
| useDeferredValue | Defer expensive renders | Responsive input with heavy rendering |
| useId | Unique IDs | Accessible form labels |

### Common Interview Questions

| Topic | Key Question |
|-------|-------------|
| Virtual DOM | How does React's reconciliation work? |
| Hooks | Explain the Rules of Hooks and why they exist |
| State | Why should you never mutate state directly? |
| useEffect | How do you prevent memory leaks in useEffect? |
| Performance | When and why would you use React.memo? |
| Context | What are the performance implications of Context? |
| Redux | When would you choose Redux over Context? |
| Keys | Why is using index as a key an anti-pattern? |
| Fiber | What is React Fiber and what problem does it solve? |
| Lifecycle | How do you replicate componentDidMount with hooks? |
| HOC vs Custom Hooks | When would you use HOC vs a custom hook? |
| Error Boundaries | What errors do error boundaries NOT catch? |
| Lazy Loading | How does React.lazy work with Suspense? |
| React 18 | What is automatic batching and useTransition? |

---

*Happy coding and interviewing! ⚛️🚀*
