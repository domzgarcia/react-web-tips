# react-web-tips, Flux and Redux tips
Collection of tips when developing a react website

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
use `concat` and `filter`
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
