# React Interview Questions with Answers (Basic to Advanced)

## **BASIC LEVEL**

### 1. What is React?
**Answer:** React is an open-source JavaScript library developed by Facebook for building user interfaces, particularly single-page applications. It allows developers to create reusable UI components and manage the view layer efficiently. React uses a virtual DOM to optimize rendering performance and follows a component-based architecture.

### 2. What are the main features of React?
**Answer:**
- **JSX (JavaScript XML):** Syntax extension that allows writing HTML-like code in JavaScript
- **Virtual DOM:** In-memory representation of the real DOM for efficient updates
- **Component-Based Architecture:** Build encapsulated components that manage their own state
- **One-Way Data Binding:** Data flows in a single direction, making the application more predictable
- **Declarative:** Describe what the UI should look like, React handles the updates
- **React Hooks:** Allow using state and lifecycle features in functional components

### 3. What is JSX?
**Answer:** JSX (JavaScript XML) is a syntax extension for JavaScript that allows you to write HTML-like code within JavaScript. It makes the code more readable and allows React to show more useful error and warning messages. JSX gets compiled to regular JavaScript function calls by tools like Babel.

```javascript
// JSX
const element = <h1>Hello, World!</h1>;

// Compiled JavaScript
const element = React.createElement('h1', null, 'Hello, World!');
```

### 4. What is the difference between functional and class components?
**Answer:**
- **Functional Components:** Simple JavaScript functions that accept props and return React elements. With hooks, they can now manage state and lifecycle methods.
- **Class Components:** ES6 classes that extend React.Component, have access to lifecycle methods and state management through this.state and this.setState.

```javascript
// Functional Component
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// Class Component
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

### 5. What are Props in React?
**Answer:** Props (properties) are read-only components that pass data from parent to child components. They are immutable and help make components reusable. Props are passed to components similar to function parameters.

```javascript
function Greeting(props) {
  return <h1>Hello, {props.name}!</h1>;
}

// Usage
<Greeting name="John" />
```

### 6. What is State in React?
**Answer:** State is a built-in object that stores property values that belong to a component. When the state object changes, the component re-renders. State is mutable and managed within the component.

```javascript
// Class component
class Counter extends React.Component {
  state = { count: 0 };
  
  increment = () => {
    this.setState({ count: this.state.count + 1 });
  }
}

// Functional component with hooks
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### 7. What is the difference between state and props?
**Answer:**
- **Props:** Passed from parent to child, immutable, used to configure components
- **State:** Managed within the component, mutable, used for data that changes over time
- Props are set by parent, state is set by the component itself
- Props cannot be modified by the child, state can be modified using setState or state setter

### 8. What are React Hooks?
**Answer:** Hooks are functions that let you use state and other React features in functional components without writing a class. They were introduced in React 16.8. Common hooks include useState, useEffect, useContext, useReducer, useCallback, useMemo, useRef, and custom hooks.

### 9. Explain useState Hook
**Answer:** useState is a Hook that allows you to add state to functional components. It returns an array with two elements: the current state value and a function to update it.

```javascript
import { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0); // Initial state is 0
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### 10. Explain useEffect Hook
**Answer:** useEffect Hook lets you perform side effects in functional components (data fetching, subscriptions, DOM manipulation). It runs after every render by default but can be controlled with dependencies.

```javascript
import { useState, useEffect } from 'react';

function Example() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    // Side effect code
    fetchData().then(result => setData(result));
    
    // Cleanup function (optional)
    return () => {
      cleanup();
    };
  }, []); // Empty array means run once on mount
}
```

---

## **INTERMEDIATE LEVEL**

### 11. What is the Virtual DOM?
**Answer:** The Virtual DOM is a lightweight copy of the actual DOM kept in memory. When state changes occur, React creates a new virtual DOM tree, compares it with the previous one (diffing), and updates only the changed parts in the real DOM (reconciliation). This approach is more efficient than updating the entire DOM.

### 12. What is the difference between controlled and uncontrolled components?
**Answer:**
- **Controlled Components:** Form data is handled by React component state. The component controls the input value through props and handles changes through event handlers.
- **Uncontrolled Components:** Form data is handled by the DOM itself. You use refs to access form values when needed.

```javascript
// Controlled
function ControlledInput() {
  const [value, setValue] = useState('');
  return <input value={value} onChange={e => setValue(e.target.value)} />;
}

// Uncontrolled
function UncontrolledInput() {
  const inputRef = useRef();
  const handleSubmit = () => console.log(inputRef.current.value);
  return <input ref={inputRef} />;
}
```

### 13. What are keys in React and why are they important?
**Answer:** Keys are special string attributes used to identify which items in a list have changed, been added, or removed. They help React optimize rendering by reusing existing elements. Keys should be stable, unique, and not array indexes when the list can be reordered.

```javascript
const items = ['Apple', 'Banana', 'Orange'];
const listItems = items.map((item, index) => 
  <li key={item}>{item}</li> // Use unique identifier, not index
);
```

### 14. What is lifting state up in React?
**Answer:** Lifting state up is a pattern where you move state to the closest common ancestor of components that need to share it. This allows multiple components to share and synchronize state.

```javascript
function Parent() {
  const [sharedState, setSharedState] = useState('');
  
  return (
    <>
      <ChildA value={sharedState} onChange={setSharedState} />
      <ChildB value={sharedState} />
    </>
  );
}
```

### 15. What is prop drilling and how can you avoid it?
**Answer:** Prop drilling is the process of passing props through multiple levels of components to reach a deeply nested component. It can be avoided using:
- Context API
- Redux or other state management libraries
- Component composition
- Custom hooks

### 16. Explain the Context API
**Answer:** Context API provides a way to pass data through the component tree without manually passing props at every level. It's designed to share data that can be considered "global" for a tree of React components.

```javascript
const ThemeContext = React.createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  const theme = useContext(ThemeContext);
  return <div className={theme}>Toolbar</div>;
}
```

### 17. What are Higher-Order Components (HOC)?
**Answer:** A Higher-Order Component is a function that takes a component and returns a new component with additional props or functionality. HOCs are used for code reuse, logic abstraction, and props manipulation.

```javascript
function withLoading(Component) {
  return function WithLoadingComponent({ isLoading, ...props }) {
    if (isLoading) return <div>Loading...</div>;
    return <Component {...props} />;
  };
}

const EnhancedComponent = withLoading(MyComponent);
```

### 18. What is useCallback Hook?
**Answer:** useCallback returns a memoized callback function. It's useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders.

```javascript
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b] // Dependencies
);
```

### 19. What is useMemo Hook?
**Answer:** useMemo returns a memoized value. It's used to optimize expensive calculations by only recomputing when dependencies change.

```javascript
const expensiveValue = useMemo(
  () => computeExpensiveValue(a, b),
  [a, b]
);
```

### 20. What is useRef Hook?
**Answer:** useRef returns a mutable ref object whose .current property is initialized with the passed argument. It persists across renders and doesn't cause re-renders when updated. Used for accessing DOM elements or storing mutable values.

```javascript
function TextInput() {
  const inputRef = useRef(null);
  
  const focusInput = () => {
    inputRef.current.focus();
  };
  
  return <input ref={inputRef} />;
}
```

---

## **REDUX BASICS**

### 21. What is Redux?
**Answer:** Redux is a predictable state container for JavaScript applications. It helps manage application state in a centralized store, making state changes predictable through pure functions called reducers. Redux follows three fundamental principles: single source of truth, state is read-only, and changes are made with pure functions.

### 22. What are the core principles of Redux?
**Answer:**
1. **Single Source of Truth:** The entire application state is stored in a single object tree within a single store
2. **State is Read-Only:** The only way to change state is to emit an action, an object describing what happened
3. **Changes are Made with Pure Functions:** To specify how the state tree is transformed by actions, you write pure reducers

### 23. What are the main components of Redux?
**Answer:**
- **Store:** Holds the application state
- **Actions:** Plain JavaScript objects that describe what happened
- **Reducers:** Pure functions that specify how state changes in response to actions
- **Dispatch:** Method to send actions to the store
- **Selectors:** Functions to extract specific data from the store

### 24. What is a Redux action?
**Answer:** An action is a plain JavaScript object that has a type property describing the action and optionally a payload containing data. Actions are the only way to send data to the store.

```javascript
// Action
const addTodo = {
  type: 'ADD_TODO',
  payload: {
    id: 1,
    text: 'Learn Redux'
  }
};

// Action Creator
function addTodo(text) {
  return {
    type: 'ADD_TODO',
    payload: { id: Date.now(), text }
  };
}
```

### 25. What is a Redux reducer?
**Answer:** A reducer is a pure function that takes the current state and an action as arguments and returns a new state. It should not mutate the state directly but return a new object with the updated state.

```javascript
const initialState = { count: 0 };

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 };
    case 'DECREMENT':
      return { count: state.count - 1 };
    default:
      return state;
  }
}
```

### 26. What is the Redux store?
**Answer:** The store is a single object that holds the entire state tree of the application. It has methods like getState() to access state, dispatch(action) to update state, and subscribe(listener) to register listeners.

```javascript
import { createStore } from 'redux';

const store = createStore(reducer);

// Get current state
const state = store.getState();

// Dispatch an action
store.dispatch({ type: 'INCREMENT' });

// Subscribe to changes
store.subscribe(() => console.log(store.getState()));
```

### 27. What is Redux Toolkit?
**Answer:** Redux Toolkit is the official, opinionated, batteries-included toolset for efficient Redux development. It includes utilities like configureStore, createSlice, createAsyncThunk, and more that simplify Redux setup and reduce boilerplate code.

```javascript
import { configureStore, createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: state => { state.value += 1; },
    decrement: state => { state.value -= 1; }
  }
});

const store = configureStore({
  reducer: { counter: counterSlice.reducer }
});
```

### 28. How do you connect React with Redux?
**Answer:** Use react-redux library which provides Provider component to pass the store to your app and hooks like useSelector and useDispatch to interact with the store.

```javascript
import { Provider, useSelector, useDispatch } from 'react-redux';

// Wrap app with Provider
<Provider store={store}>
  <App />
</Provider>

// Use in components
function Counter() {
  const count = useSelector(state => state.counter.value);
  const dispatch = useDispatch();
  
  return (
    <button onClick={() => dispatch(increment())}>
      Count: {count}
    </button>
  );
}
```

---

## **ADVANCED LEVEL**

### 29. Explain React's reconciliation algorithm
**Answer:** Reconciliation is the process React uses to update the DOM efficiently. When a component's state changes, React creates a new virtual DOM tree and compares it with the previous one using a diffing algorithm. The algorithm makes assumptions: elements of different types produce different trees, and developers can hint at which child elements may be stable across renders using keys. React then updates only the necessary parts of the actual DOM.

### 30. What are React Portals?
**Answer:** Portals provide a way to render children into a DOM node that exists outside the DOM hierarchy of the parent component. Useful for modals, tooltips, and dropdowns.

```javascript
import { createPortal } from 'react-dom';

function Modal({ children }) {
  return createPortal(
    children,
    document.getElementById('modal-root')
  );
}
```

### 31. What is React.memo and when should you use it?
**Answer:** React.memo is a higher-order component that memoizes the rendered output of a functional component. It prevents unnecessary re-renders by doing a shallow comparison of props. Use it for components that render often with the same props.

```javascript
const MyComponent = React.memo(function MyComponent({ data }) {
  return <div>{data}</div>;
}, (prevProps, nextProps) => {
  // Custom comparison function (optional)
  return prevProps.data === nextProps.data;
});
```

### 32. Explain useReducer Hook
**Answer:** useReducer is an alternative to useState for managing complex state logic. It accepts a reducer function and initial state, returning the current state and a dispatch function. Preferable when state logic is complex or when next state depends on previous.

```javascript
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
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </>
  );
}
```

### 33. What are Error Boundaries?
**Answer:** Error Boundaries are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI. They catch errors during rendering, in lifecycle methods, and in constructors. Note: Error boundaries do not catch errors in event handlers, async code, or errors in the error boundary itself.

```javascript
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    logErrorToService(error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

### 34. What is code splitting in React?
**Answer:** Code splitting is a technique to split your code into smaller bundles that can be loaded on demand, reducing initial load time. Implemented using dynamic import() and React.lazy().

```javascript
import React, { lazy, Suspense } from 'react';

const LazyComponent = lazy(() => import('./LazyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

### 35. What is the difference between useEffect and useLayoutEffect?
**Answer:**
- **useEffect:** Runs asynchronously after the DOM has been painted. Doesn't block visual updates. Use for most side effects.
- **useLayoutEffect:** Runs synchronously after DOM mutations but before the browser paints. Blocks visual updates. Use when you need to read layout from the DOM and synchronously re-render.

### 36. What are custom hooks?
**Answer:** Custom hooks are JavaScript functions whose names start with "use" and may call other hooks. They let you extract component logic into reusable functions.

```javascript
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });
  
  const setValue = value => {
    setStoredValue(value);
    window.localStorage.setItem(key, JSON.stringify(value));
  };
  
  return [storedValue, setValue];
}
```

### 37. What is Redux middleware?
**Answer:** Middleware provides a third-party extension point between dispatching an action and the moment it reaches the reducer. Used for logging, crash reporting, async actions, routing, etc. Redux Thunk and Redux Saga are popular middleware.

```javascript
// Custom middleware
const logger = store => next => action => {
  console.log('dispatching', action);
  let result = next(action);
  console.log('next state', store.getState());
  return result;
};

const store = createStore(reducer, applyMiddleware(logger));
```

### 38. What is Redux Thunk?
**Answer:** Redux Thunk is middleware that allows you to write action creators that return a function instead of an action object. The function receives dispatch and getState as arguments, enabling async logic and conditional dispatching.

```javascript
// Thunk action creator
function fetchUser(id) {
  return async (dispatch, getState) => {
    dispatch({ type: 'FETCH_USER_REQUEST' });
    
    try {
      const response = await fetch(`/api/users/${id}`);
      const data = await response.json();
      dispatch({ type: 'FETCH_USER_SUCCESS', payload: data });
    } catch (error) {
      dispatch({ type: 'FETCH_USER_FAILURE', error });
    }
  };
}

// Usage
dispatch(fetchUser(123));
```

### 39. What is Redux Saga?
**Answer:** Redux Saga is a middleware library that uses ES6 Generators to make side effects easier to manage, test, and handle failures. Sagas are functions that watch for specific actions and perform side effects.

```javascript
import { call, put, takeEvery } from 'redux-saga/effects';

function* fetchUserSaga(action) {
  try {
    const user = yield call(api.fetchUser, action.payload.userId);
    yield put({ type: 'FETCH_USER_SUCCESS', user });
  } catch (e) {
    yield put({ type: 'FETCH_USER_FAILURE', message: e.message });
  }
}

function* watchFetchUser() {
  yield takeEvery('FETCH_USER_REQUEST', fetchUserSaga);
}
```

### 40. What is createAsyncThunk in Redux Toolkit?
**Answer:** createAsyncThunk is a function that accepts an action type string and a callback that returns a promise. It automatically generates pending, fulfilled, and rejected action types and dispatches them based on the promise lifecycle.

```javascript
import { createAsyncThunk, createSlice } from '@reduxjs/toolkit';

const fetchUserById = createAsyncThunk(
  'users/fetchById',
  async (userId, thunkAPI) => {
    const response = await fetch(`/api/users/${userId}`);
    return response.json();
  }
);

const usersSlice = createSlice({
  name: 'users',
  initialState: { entities: [], loading: 'idle' },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchUserById.pending, (state) => {
        state.loading = 'loading';
      })
      .addCase(fetchUserById.fulfilled, (state, action) => {
        state.entities.push(action.payload);
        state.loading = 'idle';
      })
      .addCase(fetchUserById.rejected, (state) => {
        state.loading = 'failed';
      });
  }
});
```

### 41. What are Redux selectors and Reselect?
**Answer:** Selectors are functions that extract and derive data from the Redux store state. Reselect is a library for creating memoized selectors that compute derived data efficiently, only recalculating when relevant state changes.

```javascript
import { createSelector } from 'reselect';

const selectTodos = state => state.todos;
const selectFilter = state => state.filter;

const selectVisibleTodos = createSelector(
  [selectTodos, selectFilter],
  (todos, filter) => {
    switch (filter) {
      case 'COMPLETED':
        return todos.filter(t => t.completed);
      case 'ACTIVE':
        return todos.filter(t => !t.completed);
      default:
        return todos;
    }
  }
);
```

### 42. What is the difference between Redux and Context API?
**Answer:**
- **Redux:** Full-featured state management with middleware, dev tools, time-travel debugging, better performance for frequent updates, steeper learning curve
- **Context API:** Built into React, simpler API, good for simple global state, can cause performance issues with frequent updates, no middleware or dev tools

Use Context for simple global state (theme, auth), use Redux for complex state management with async logic and multiple updates.

### 43. What is React Suspense?
**Answer:** Suspense is a component that lets you specify a loading state while waiting for child components to load. Currently works with React.lazy for code splitting and will support data fetching in future versions.

```javascript
const ProfilePage = lazy(() => import('./ProfilePage'));

<Suspense fallback={<Spinner />}>
  <ProfilePage />
</Suspense>
```

### 44. What is the Flux architecture?
**Answer:** Flux is an application architecture pattern for building user interfaces with unidirectional data flow. It consists of:
- **Actions:** Objects describing what happened
- **Dispatcher:** Central hub managing all actions
- **Stores:** Contain application state and logic
- **Views:** React components that re-render based on store changes

Redux is an implementation inspired by Flux but with a single store and pure reducers.

### 45. What are render props?
**Answer:** Render props is a technique for sharing code between components using a prop whose value is a function. The component with a render prop calls this function instead of implementing its own render logic.

```javascript
class MouseTracker extends React.Component {
  state = { x: 0, y: 0 };
  
  handleMouseMove = (event) => {
    this.setState({ x: event.clientX, y: event.clientY });
  };
  
  render() {
    return (
      <div onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    );
  }
}

// Usage
<MouseTracker render={({ x, y }) => (
  <h1>Mouse position: {x}, {y}</h1>
)} />
```

### 46. What is React.forwardRef?
**Answer:** forwardRef is a function that lets a component pass a ref through to a child component. Useful for accessing DOM nodes or component instances of child components.

```javascript
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="fancy-button">
    {props.children}
  </button>
));

// Usage
const ref = React.createRef();
<FancyButton ref={ref}>Click me</FancyButton>
```

### 47. What are the performance optimization techniques in React?
**Answer:**
- Use React.memo for component memoization
- Use useMemo for expensive calculations
- Use useCallback to memoize functions
- Implement code splitting with React.lazy
- Use virtualization for long lists (react-window, react-virtualized)
- Avoid inline function definitions in render
- Use production build for deployment
- Implement proper key props for lists
- Use React DevTools Profiler
- Avoid unnecessary re-renders with proper state structure

### 48. Explain the concept of immutability in Redux
**Answer:** Immutability means never modifying existing objects or arrays directly. Instead, create new copies with the desired changes. This is crucial in Redux because:
- Enables efficient change detection (reference comparison)
- Prevents unintended side effects
- Enables time-travel debugging
- Makes state changes predictable

```javascript
// Wrong - mutating state
state.user.name = 'John';

// Correct - immutable update
return {
  ...state,
  user: {
    ...state.user,
    name: 'John'
  }
};
```

### 49. What is normalizing state shape in Redux?
**Answer:** Normalizing state means structuring nested data in a flat, relational-database-like format. Store items in an object keyed by ID instead of nested arrays. Benefits include easier updates, no duplication, and better performance.

```javascript
// Non-normalized
const state = {
  posts: [
    { id: 1, author: { id: 1, name: 'User 1' }, comments: [...] }
  ]
};

// Normalized
const state = {
  posts: {
    byId: { 1: { id: 1, authorId: 1, commentIds: [1, 2] } },
    allIds: [1]
  },
  authors: {
    byId: { 1: { id: 1, name: 'User 1' } },
    allIds: [1]
  },
  comments: {
    byId: { 1: {...}, 2: {...} },
    allIds: [1, 2]
  }
};
```

### 50. What is RTK Query?
**Answer:** RTK Query is a powerful data fetching and caching tool built into Redux Toolkit. It simplifies API calls and cache management with auto-generated hooks, automatic re-fetching, optimistic updates, and cache invalidation.

```javascript
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

const api = createApi({
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
  endpoints: (builder) => ({
    getPosts: builder.query({
      query: () => 'posts'
    }),
    addPost: builder.mutation({
      query: (body) => ({
        url: 'posts',
        method: 'POST',
        body
      })
    })
  })
});

// Auto-generated hooks
const { data, error, isLoading } = useGetPostsQuery();
const [addPost] = useAddPostMutation();
```

---

This comprehensive list covers React and Redux concepts from basic to advanced levels, providing you with solid interview preparation material.
