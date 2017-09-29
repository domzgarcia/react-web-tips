# react-web-tips
Collection of tips when developing a react website

### Navigation Tips
* use through history `let {history} = this.props; history.isActive('home') // true or false`
* use `<Link>` tag with `<Link activeClass={path}>` attribute
* to ignore or parameter params or query parameter, enclose path in Route ex: 
  <br /> `<Route path="archives(/:article)" name="archives" component={Archives}></Route>`

