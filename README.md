# react-web-tips
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

