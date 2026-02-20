# Redux

### Table of Contents
1. [What is redux](#1-what-is-redux)
2. [What is redux Toolkit](#2-what-is-redux-toolkit)
3. [Differences with Redux vs Redux Toolkit](#3-differences-with-redux-vs-redux-toolkit)
4. [Two Slices](#4-two-slices)
5. 


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
