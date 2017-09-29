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

### Assigning Object
* `Object.assign $.extend _.extend _.assign`

### Immutable through referencing in Object
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
