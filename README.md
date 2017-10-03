# react-web-tips, Flux and Redux tips
Collection of tips when developing a react website `NOT YET VISITED BY ME` [pen](https://codepen.io/ericb81/) [egg](https://egghead.io/lessons/javascript-redux-simplifying-the-arrow-functions) [dan](https://egghead.io/instructors/dan-abramov)

### Navigation Tips
* use through history `let {history} = this.props; history.isActive('home') // true or false`
* use `<Link>` tag with `<Link activeClass={path}>` attribute
* to ignore or url params or query params, enclose path attribute in your Route like this
  <br /> ex: `<Route path="archives(/:article)" name="archives" component={Archives}></Route>`
  
### Style
* passing style to attribute style in the markup
```
const containerStyle = {
  marginTop: "60px"
};
<div class="container" style={containerStyle}></div>
```
* snakeCasing is smart enough to identify "margin-top" css

### Immutable through referencing in Object
use `Object.assign $.extend _.extend _.assign`
```
var a = {name:"Will", age: 35};
var b = Object.assign({}, a, {name: "Fred"});
b // Object {name: "Fred", age: 35}
a // Object {name: "Will", age: 35 }
```

### Immutable through referencing in Array
use `concat, filter map` and `reduce`
```
var a = [0,1,2]
var b = a.concat(3)
a // [0,1,2]
b // [0,1,2,3]
var c = a.filter((val) => val !== 2)
c // [0,1]
a // [0,1,2]
```
### Explained simple redux manifesto
```
import {createStore} from 'redux';
const reducer = function(state, action){
    let {type, payload} = action;
    if(type === "INC"){
        return state+=payload;
    }
    if(type === "DEC"){
        return state-=payload;
    }
    return state;
}
const store = createStore(reducer, 0);
store.subscribe( () => {
    console.log('hey store changed', store.getState())
});
store.dispatch({type: "INC", payload: 1});
store.dispatch({type: "INC", payload: 2});
store.dispatch({type: "INC", payload: 22});
store.dispatch({type: "INC", payload: 1});
store.dispatch({type: "DEC", payload: 1000});
```

### Another Sample, setting your state Immutably
```
import {combineReducers, createStore} from 'redux';
const userReducer = (state = {}, action) => {
    switch(action.type){
        case 'CHANGE_USER': 
            state = {...state, name: action.payload}
        break;
        case 'CHANGE_AGE': 
            state = {...state, age: action.payload}
        break;
    }
    return state;
};
const tweetsReducer = (state =[], action) => {
    let arr = [];
    switch(action.type){
        case 'CHANGE_USER':
        arr = state.concat('entry');
        break;
    }
    return (arr.length === 0) ? state : arr;
};
const reducer = combineReducers({
    user: userReducer,
    tweets: tweetsReducer
});
const store = createStore(reducer);
store.subscribe( () => {
    console.log('hey store changed', store.getState())
});
store.dispatch({type: 'CHANGE_USER', payload: 'Dom'});
store.dispatch({type: 'CHANGE_USER', payload: 'Dom'});
store.dispatch({type: 'CHANGE_AGE', payload: 35});
```
### Simple Middleware
```
import {applyMiddleware, combineReducers, createStore} from 'redux';
const userReducer = (state = {}, action) => {
    switch(action.type){
        case 'CHANGE_USER': 
            state = {...state, name: action.payload}
        break;
        case 'CHANGE_AGE': 
            state = {...state, age: action.payload}
        break;
    }
    return state;
};
const tweetsReducer = (state =[], action) => {
    let arr = [];
    switch(action.type){
        case 'CHANGE_USER':
        arr = state.concat('entry');
        break;
        case 'ERROR': 
            throw new Error('Intentionally Error!');
        break;
    }
    return (arr.length === 0) ? state : arr;
};
const reducer = combineReducers({
    user: userReducer,
    tweets: tweetsReducer
});
const logger = (store) => (next) => (action) => {
    console.log("action fired", action);
    // action.type = "CHANGE_AGE";
    next(action);
};
const error = (store) => (next) => (action) => {
    try {
        next(action);
    } catch(e){
        console.log('Exception', e);
    }
}
const middleware = applyMiddleware(logger, error);
const store = createStore(reducer, middleware);
store.subscribe( () => {
    console.log('hey store changed', store.getState())
});
store.dispatch({type: 'CHANGE_USER', payload: 'Dom'});
store.dispatch({type: 'CHANGE_USER', payload: 'Dom'});
store.dispatch({type: 'CHANGE_AGE', payload: 35});
store.dispatch({type: 'ERROR', payload: 35});
```
### Redux Thunk
```
import {applyMiddleware, createStore} from 'redux';
import axios from 'axios';
import logger from 'redux-logger';
import thunk from 'redux-thunk';
const initialState = {
    fetching: false,
    fetched: false,
    users: [],
    error: null
};
const reducer = (state = initialState, action) => {
    switch(action.type){
        case "FETCH_USERS_START":{
            return { ...state, fetching: true};
            break;
        }
        case "FETCH_USERS_ERROR": {
            return { ...state, fetching: false, error: action.payload};
            break;
        }
        case "RECEIVE_USERS": {
            return { ...state, fetching: false, fetched: true, users: action.payload};
            break;
        }
    }
    return state;
}
const middleware = applyMiddleware(thunk, logger());
const store = createStore(reducer, middleware);
store.dispatch( (dispatch) => {
    dispatch({type: "FETCH_USERS_START"});

    axios.get("http://rest.learncode.academy/api/wstern/users")
        .then( (response) =>{
            dispatch({type: "RECEIVE_USERS", payload: response.data})
        })
        .catch( (err) => {
            dispatch({type: "FETCH_USERS_ERROR", payload: err})
        });
});
```
### Redux Promise
```
import {applyMiddleware, createStore} from 'redux';
import axios from 'axios';
import logger from 'redux-logger';
import thunk from 'redux-thunk';
import promise from 'redux-promise-middleware';

const initialState = {
    fetching: false,
    fetched: false,
    users: [],
    error: null
};
const reducer = (state = initialState, action) => {
    switch(action.type){
        case "FETCH_USERS_START":{
            return { ...state, fetching: true};
            break;
        }
        case "FETCH_USERS_ERROR": {
            return { ...state, fetching: false, error: action.payload};
            break;
        }
        case "RECEIVE_USERS": {
            return { ...state, fetching: false, fetched: true, users: action.payload};
            break;
        }
    }
    return state;
}
const middleware = applyMiddleware(promise(), logger());

const store = createStore(reducer, middleware);
store.dispatch({
    type: "FOO",
    payload: axios.get("http://rest.learncode.academy/api/wstern/users")
});
```
### Changing Values with DOT (.) notation
```
import {createStore} from 'redux';
const initialState = {
    result: 1,
    lastValues: [],
    username: "Max"
}
const reducer = (state = initialState, action) =>{
    
    switch(action.type){
        case 'ADD':
            state = {
                ...state, 
                result: state.result + action.payload,
                lastValues: [...state.lastValues, action.payload] // or lastValues: state.lastValues.concat(action.payload)
            };
            // wrong: state.lastValues.push(action.payload);
            // wrong: state.result += action.payload;
        break;
        case 'SUBTRACT':
            state = {
                ...state, 
                result: state.result - action.payload,
                lastValues: [...state.lastValues, action.payload] // or lastValues: state.lastValues.concat(action.payload)
            };
            // wrong: state.lastValues.push(action.payload);
            // wrong: state.result -= action.payload;
        break;
    }
    return state;
};
const store = createStore(reducer);
store.subscribe( () => {
    console.log('Store updated!', store.getState())
});
store.dispatch({type: "ADD", payload: 100});
store.dispatch({type: "ADD", payload: 22});
store.dispatch({type: "SUBTRACT", payload: 80});
```
### Custom Logger
```
import {createStore, combineReducers, applyMiddleware} from 'redux';
const mathReducer = (state = {
    result: 1,
    lastValues: [] 
    }, 
    action) =>{
    switch(action.type){
        case 'ADD':
            state = {
                ...state,
                result: state.result + action.payload,
                lastValues: [...state.lastValues, action.payload]
            };
        break;
        case 'SUBTRACT':
            state = {
                ...state,
                result: state.result - action.payload,
                lastValues: [...state.lastValues, action.payload]
            };
        break;
    }
    return state;
};
const userReducer = (state = {
    name: "Max",
    age: 27 
    }, 
    action) =>{
    switch(action.type){
        case 'SET_NAME':
            state = {
                ...state,
                name: action.payload,
            };
        break;
        case 'SET_AGE':
            state = {
                ...state,
                age: action.payload,
            };
        break;
    }
    return state;
};
const myLogger = (store) => (next) => (action) => {
    console.log('Logged Action:', action);
    next(action);
};
const store = createStore( combineReducers({mathReducer, userReducer}), {}, applyMiddleware(myLogger) );
store.subscribe( () => {
    console.log('Store updated!', store.getState())
});
store.dispatch({type: "ADD", payload: 100});
store.dispatch({type: "ADD", payload: 22});
store.dispatch({type: "SUBTRACT", payload: 80});
store.dispatch({type: "SET_AGE", payload: 30});
```
### Redux Logger
```
import {createStore, combineReducers, applyMiddleware} from 'redux';
import logger from 'redux-logger';

const mathReducer = (state = {
    result: 1,
    lastValues: [] 
    }, 
    action) =>{
    switch(action.type){
        case 'ADD':
            state = {
                ...state,
                result: state.result + action.payload,
                lastValues: [...state.lastValues, action.payload]
            };
        break;
        case 'SUBTRACT':
            state = {
                ...state,
                result: state.result - action.payload,
                lastValues: [...state.lastValues, action.payload]
            };
        break;
    }
    return state;
};
const userReducer = (state = {
    name: "Max",
    age: 27 
    }, 
    action) =>{
    switch(action.type){
        case 'SET_NAME':
            state = {
                ...state,
                name: action.payload,
            };
        break;
        case 'SET_AGE':
            state = {
                ...state,
                age: action.payload,
            };
        break;
    }
    return state;
};
const myLogger = (store) => (next) => (action) => {
    console.log('Logged Action:', action);
    next(action);
};
const store = createStore( combineReducers({mathReducer, userReducer}), {}, applyMiddleware( myLogger, logger() ));
store.subscribe( () => {
    console.log('Store updated!', store.getState())
});
store.dispatch({type: "ADD", payload: 100});
store.dispatch({type: "ADD", payload: 22});
store.dispatch({type: "SUBTRACT", payload: 80});
store.dispatch({type: "SET_AGE", payload: 30});
```
