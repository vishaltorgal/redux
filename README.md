# Redux

### Table of Contents
1. [What is redux](#1-what-is-redux)
2. [What is redux Toolkit](#2-what-is-redux-toolkit)
3. [Differences with Redux vs Redux Toolkit](#3-differences-with-redux-vs-redux-toolkit)
4. [Two Slices](#4-two-slices)
5. [Extra Reducer](#5-extra-reducer)
6. [createEntityAdapter](#6-createentityadapter)


## 1. What is redux

***Redux*** is a state management library used to manage global application state in a predictable way.

Redux stores the entire app state in one central store and updates it using actions and reducers.

***Core concepts***
- Store → where state lives
- Action → what happened
- Reducer → how state changes

### Folder Structure
```jsx
src/
├── app/
│   └── store.js           # Redux store setup
├── features/
│   └── counter/
│       ├── counterActions.js   # Action types & creators
│       ├── counterReducer.js   # Reducer
│       └── CounterComponent.jsx # React component
└── index.js               # Entry point (ReactDOM.render)
```
### Redux Flow
```jsx
// src/features/counter/counterActions.js
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';

export const increment = () => ({ type: INCREMENT });
export const decrement = () => ({ type: DECREMENT });

// src/features/counter/counterReducer.js
import { INCREMENT, DECREMENT } from './counterActions';

const initialState = { value: 0 };

export function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { ...state, value: state.value + 1 };
    case DECREMENT:
      return { ...state, value: state.value - 1 };
    default:
      return state;
  }
}

// src/app/store.js
import { createStore, combineReducers } from 'redux';
import { counterReducer } from '../features/counter/counterReducer';

const rootReducer = combineReducers({
  counter: counterReducer
});

export const store = createStore(rootReducer);
```

### CounterComponent same for both
***src/features/counter/CounterComponent.jsx***
```jsx
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './counterSlice';

export default function CounterComponent() {
  const value = useSelector(state => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>{value}</h1>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  );
}
```
### index.js same for both
***src/index.js***
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';
import { store } from './app/store';
import CounterComponent from './features/counter/CounterComponent';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <Provider store={store}>
    <CounterComponent />
  </Provider>
);
```

## 2. What is redux Toolkit

### Folder Structure (Redux Toolkit)
```jsx
src/
├── app/
│   └── store.js                  # Redux Toolkit store setup
├── features/
│   └── counter/
│       ├── counterSlice.js       # Slice: actions + reducer
│       └── CounterComponent.jsx  # React component for this feature
└── index.js                       # React entry point
```

### Redux Toolkit Flow
```jsx
// src/features/counter/counterSlice.js
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1 }, // Immer allows "mutating" code
    decrement: (state) => { state.value -= 1 }
  }
});

// Export actions and reducer
export const { increment, decrement } = counterSlice.actions;
export const counterReducer = counterSlice.reducer;

// src/app/store.js
import { configureStore } from '@reduxjs/toolkit';
import { counterReducer } from '../features/counter/counterSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer
  }
});
```

### CounterComponent same for both
***src/features/counter/CounterComponent.jsx***
```jsx
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './counterSlice';

export default function CounterComponent() {
  const value = useSelector(state => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>{value}</h1>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  );
}
```
### index.js same for both
***src/index.js***
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';
import { store } from './app/store';
import CounterComponent from './features/counter/CounterComponent';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <Provider store={store}>
    <CounterComponent />
  </Provider>
);
```


## 3. Differences with Redux vs Redux Toolkit

| Feature / Aspect                   | Classic Redux                                                             | Redux Toolkit (RTK)                                                                 |
| ---------------------------------- | ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **Release Year**                   | 2015                                                                      | 2019 (official modern wrapper for Redux)                                            |
| **Boilerplate**                    | High — need separate action types, action creators, reducers, store setup | Low — `createSlice` combines reducer + actions, `configureStore` simplifies store   |
| **Reducers**                       | Written with `switch-case` statements and immutable updates               | Written with `createSlice` and "mutable-looking" updates (handled safely via Immer) |
| **Action Types**                   | Manually created as constants                                             | Automatically generated from slice name + reducer key                               |
| **Action Creators**                | Manually written                                                          | Automatically generated by `createSlice`                                            |
| **Store Setup**                    | `createStore` + manual middleware setup                                   | `configureStore` includes DevTools, Thunk, and default middleware automatically     |
| **Async Logic**                    | Needs separate middleware like `redux-thunk`                              | Built-in support using `createAsyncThunk`                                           |
| **State Mutations**                | Must be immutable (spread operator / Object.assign)                       | Can write "mutating" code; Immer handles immutability                               |
| **Scalability**                    | Can get verbose for multiple features; manual combineReducers             | Designed for scalable slices; encourages feature-based structure                    |
| **DevTools Integration**           | Manual setup                                                              | Built-in                                                                            |
| **Best Practice / Recommendation** | Older method; more learning value                                         | Modern standard; recommended for all new Redux projects                             |
| **Learning Curve**                 | Higher due to boilerplate                                                 | Lower, easier to read and maintain                                                  |


## 4. Two Slices

Redux Toolkit example using two slices: counterSlice and colorSlice

### Folder Structure
```jsx
src/
├── app/
│   └── store.js
├── features/
│   ├── counter/
│   │   └── counterSlice.js
│   ├── color/
│   │   └── colorSlice.js
│   └── CounterComponent.jsx
└── index.js
```

### counterSlice.js
   
```jsx
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1 },
    decrement: (state) => { state.value -= 1 },
    reset: (state) => { state.value = 0 }
  }
});

export const { increment, decrement, reset } = counterSlice.actions;
export const counterReducer = counterSlice.reducer;
```

### colorSlice.js
```jsx
import { createSlice } from '@reduxjs/toolkit';

const colorSlice = createSlice({
  name: "color",
  initialState: { color: "blue" },
  reducers: {
    changeColor: (state, action) => { state.color = action.payload }
  }
});

export const { changeColor } = colorSlice.actions;
export const colorReducer = colorSlice.reducer;
```

### store.js
```jsx
import { configureStore } from '@reduxjs/toolkit';
import { counterReducer } from '../features/counter/counterSlice';
import { colorReducer } from '../features/color/colorSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    color: colorReducer
  }
});
```

### CounterComponent.jsx
```jsx
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement, reset } from './counter/counterSlice';
import { changeColor } from './color/colorSlice';

export default function CounterComponent() {
  const count = useSelector(state => state.counter.value);
  const color = useSelector(state => state.color.color);
  const dispatch = useDispatch();

  return (
    <div>
      <h1 style={{ color }}>{count}</h1>
      <div style={{ marginBottom: "10px" }}>
        <button onClick={() => dispatch(increment())}>+</button>
        <button onClick={() => dispatch(decrement())}>-</button>
        <button onClick={() => dispatch(reset())}>Reset</button>
      </div>
      <div>
        <button onClick={() => dispatch(changeColor("red"))}>Red</button>
        <button onClick={() => dispatch(changeColor("green"))}>Green</button>
        <button onClick={() => dispatch(changeColor("blue"))}>Blue</button>
      </div>
    </div>
  );
}
```

### index.js
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';
import { store } from './app/store';
import CounterComponent from './features/CounterComponent';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <Provider store={store}>
    <CounterComponent />
  </Provider>
);
```

## 5. Extra Reducer

- Think of reducers as your slice's "private" logic—they only respond to actions defined inside that same slice.
- ExtraReducers, on the other hand, are the "public" listeners. they allow your slice to respond to actions that were defined outside of it, such as actions from other slices or asynchronous tasks (like fetching data).

Simple Redux Toolkit example with API call using createAsyncThunk and extraReducers.

### userSlice.js
```jsx
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// Async API call using createAsyncThunk
export const fetchUsers = createAsyncThunk(
  'users/fetchUsers',  //action type prefix
  async () => {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    if (!response.ok) throw new Error('Failed to fetch users');
    return response.json(); // returned data becomes action.payload
  }
);

const userSlice = createSlice({
  name: 'users',
  initialState: { list: [], status: 'idle', error: null },
  reducers: {}, // no regular reducers needed for this example
  extraReducers: (builder) => {
    builder
      .addCase(fetchUsers.pending, (state) => { state.status = 'loading'; })
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.status = 'succeeded';
        state.list = action.payload;
      })
      .addCase(fetchUsers.rejected, (state, action) => {
        state.status = 'failed';
        state.error = action.error.message;
      });
  }
});

export const userReducer = userSlice.reducer;
```

### store.js
```jsx
import { configureStore } from '@reduxjs/toolkit';
import { userReducer } from './userSlice';

export const store = configureStore({
  reducer: { users: userReducer }
});
```

### UserListComponent.jsx
```jsx
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { fetchUsers } from './userSlice';

export default function UserList() {
  const { list, status, error } = useSelector(state => state.users);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchUsers()); // call API on component mount
  }, [dispatch]);

  if (status === 'loading') return <p>Loading...</p>;
  if (status === 'failed') return <p>Error: {error}</p>;

  return (
    <div>
      <h2>Users List</h2>
      <ul>
        {list.map(user => (
          <li key={user.id}>{user.name} ({user.email})</li>
        ))}
      </ul>
    </div>
  );
}
```

### index.js
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';
import { store } from './store';
import UserList from './UserListComponent';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <Provider store={store}>
    <UserList />
  </Provider>
);
```

### ✅ Explanation

- createAsyncThunk handles async API calls and generates pending, fulfilled, rejected actions.
- extraReducers listens to those actions to update the state (status, list, error).
- Component uses useEffect to dispatch the async thunk when mounted.
- Redux Toolkit + createAsyncThunk removes manual dispatching of multiple actions and avoids boilerplate.


### 'users/fetchUsers' is the “prefix” for the action type.
Redux Toolkit automatically creates three action types for the async flow:

| Lifecycle | Generated Action Type          |
| --------- | ------------------------------ |
| Pending   | `'users/fetchUsers/pending'`   |
| Fulfilled | `'users/fetchUsers/fulfilled'` |
| Rejected  | `'users/fetchUsers/rejected'`  |


## 6. createEntityAdapter

### userSlice.js
```jsx
import { createSlice, createEntityAdapter } from '@reduxjs/toolkit';

// 1. Create an adapter
const usersAdapter = createEntityAdapter();

// 2. Create the slice
const userSlice = createSlice({
  name: 'users',
  initialState: usersAdapter.getInitialState(),
  reducers: {
    addUser: usersAdapter.addOne,
    removeUser: usersAdapter.removeOne
  }
});

// 3. Export actions and reducer
export const { addUser, removeUser } = userSlice.actions;
export const userReducer = userSlice.reducer;

// 4. Export selectors
export const { selectAll: selectAllUsers } = usersAdapter.getSelectors(state => state.users);
```

### store.js
```jsx
import { configureStore } from '@reduxjs/toolkit';
import { userReducer } from './userSlice';

export const store = configureStore({
  reducer: { users: userReducer }
});
```

### UserListComponent.jsx
```jsx
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { addUser, removeUser, selectAllUsers } from './userSlice';

export default function UserList() {
  const users = useSelector(selectAllUsers);
  const dispatch = useDispatch();

  return (
    <div>
      <h2>Users</h2>
      <ul>
        {users.map(u => <li key={u.id}>{u.name} <button onClick={() => dispatch(removeUser(u.id))}>Delete</button></li>)}
      </ul>
      <button onClick={() => dispatch(addUser({ id: Date.now(), name: 'New User' }))}>
        Add User
      </button>
    </div>
  );
}
```

### ✅ How it works
createEntityAdapter stores users like this:
```jsx
{
  ids: [101, 102],
  entities: {
    101: { id: 101, name: 'Alice' },
    102: { id: 102, name: 'Bob' }
  }
}
```

### Common Reducer Functions

| Helper                      | What it does                                      |
| --------------------------- | ------------------------------------------------- |
| `addOne(state, action)`     | Adds a single entity (`action.payload`)           |
| `addMany(state, action)`    | Adds multiple entities (`action.payload` array)   |
| `setAll(state, action)`     | Replaces all entities with `action.payload` array |
| `updateOne(state, action)`  | Updates a single entity using `{ id, changes }`   |
| `updateMany(state, action)` | Updates multiple entities                         |
| `removeOne(state, action)`  | Removes entity by ID (`action.payload`)           |
| `removeMany(state, action)` | Removes multiple entities by ID                   |
| `upsertOne(state, action)`  | Adds or updates one entity                        |
| `upsertMany(state, action)` | Adds or updates multiple entities                 |

- dispatch(addUser({ id: 1, name: 'Alice' })) → adds a new user
- dispatch(removeUser(1)) → removes the user with id = 1
- ❌ They do NOT modify your database.
- ✅ They only modify the Redux store (frontend state in memory).
- ✅ Update the Redux state
- ✅ Change ids and entities
- ❌ Do NOT call an API
- ❌ Do NOT touch your backend/database
- ❌ Do NOT persist anything automatically

### 🔥 Real Production Flow
1️⃣ Call Backend API
```jsx
DELETE /api/users/5
```

2️⃣ If API succeeds → Update Redux store
```jsx
builder.addCase(deleteUser.fulfilled, (state, action) => {
  usersAdapter.removeOne(state, action.payload);
});
```
Now UI updates.

### 🧠 Why This Order?
`Because:`

- Database is the source of truth
- Redux is just a frontend copy
- You don’t want to remove it from UI if backend fails.

### 🧱 Full Real Example

Async thunk
```jsx
export const deleteUser = createAsyncThunk(
  "users/deleteUser",
  async (id) => {
    await fetch(`/api/users/${id}`, { method: "DELETE" });
    return id; // return id so reducer can remove it
  }
);
```

In slice
```jsx
extraReducers: (builder) => {
  builder.addCase(deleteUser.fulfilled, (state, action) => {
    usersAdapter.removeOne(state, action.payload);
  });
}
```
