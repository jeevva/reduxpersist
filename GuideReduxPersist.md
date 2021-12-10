# How implement Redux Persist X Redux Toolkit

## 1. Redux Persist (Local Storage)

### Step 1 - Install Redux persist

```javascript
// install redux-persist
npm install redux-persist --save
// install redux-toolkit
npm install @reduxjs/toolkit --save
```

### Step 2 - Prepare your Dir

![DIR](./Dir.PNG)

1. /src/index.js (Main index)
2. /src/store/ index.js (store configuration)
3. /src/store/reducer/auth.js (reducer auth , "just example")
4. /src/store/reducer/index.js (for combineReducers)

### Step 3 - Create Reducer example => Auth Reducer

dir : /src/store/reducer/auth.js

```javascript
import { createSlice } from "@reduxjs/toolkit";

const initialState = {
    isAuth:false,
    users: {},
};
const authSlice = createSlice ({
    name : "auth",
    initialState : initialState,
    reducers: {
        allUsers(state,data) {
            state.users = data.payload;
        },
        login : (state, data) =>{
            state.users = data.payload;
        }
    }
})

export const authActions = authSlice.actions;
export default authSlice.reducer;
```

### Step 4 - Grouping your reducer with 'combineReducers'

dir : /src/store/reducer/index.js

```javascript
import { combineReducers } from "redux";
import auth from "./auth";
const reducers = combineReducers({
    auth: auth,
    //add another reducer, if you need
});
export default reducers;
```

### Step 5 - Config your store 

dir : /src/store/index.js

```javascript
import {configureStore} from '@reduxjs/toolkit';
import storage from 'redux-persist/lib/storage';// default for Local Storage
import { persistReducer } from 'redux-persist';
import thunk from 'redux-thunk';
import reducers from './reducer';
//Configuration persist your key name, whitelist and blacklist reducer, and storage Engine
const persistConfig = {
    key: 'root',
    storage,
  whitelist:['auth'], //which you want to keep eternal with redux persist :)
  blacklist:['anything-you-want'],//redux list you don't want to keep :(
};

//Set reducer and persist config
const persistedReducer = persistReducer(persistConfig, reducers); 

//Configuration Store
const store = configureStore({
    reducer: persistedReducer,
    middleware: [thunk]
});

export default store;
```

### Step 6 - Set up your main index

dir : /src/index.js

```javascript
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import "bootstrap/dist/css/bootstrap.min.css";
import App from "./containers/screens/App";
import { BrowserRouter } from "react-router-dom";
import reportWebVitals from "./reportWebVitals";
import { Provider } from "react-redux";
import store from "./store";
import { persistStore } from "redux-persist";
import { PersistGate } from "redux-persist/integration/react";

let persistor = persistStore(store);
ReactDOM.render(
    <Provider store={store}>
    	{/* PersistGate delays the rendering of your app's UI until your persisted state has been retrieved and saved to redux. */}
        <PersistGate loading={null} persistor={persistor}>
            <BrowserRouter>
                <App />
            </BrowserRouter>
        </PersistGate>
    </Provider>,
    document.getElementById("root")
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();

```

### Step 7 - Call your state

Example: 

```javascript
import { useSelector } from "react-redux";
import { authActions } from "../../../store/reducer/auth";
export default function LoginForm() {
  const users = useSelector((state) => {
    return state.auth.users;
  });
  console.log (users)
  }
  .......
```

### Step 8 - Run your app

```
npm start
```

> For Checking
>
> Inspect > application >LocalStorage
![LocalStorage](./LocalStorage.PNG)
Good Luck , Happy codinggg ..
