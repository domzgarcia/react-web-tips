# react-web-tips
Collection of tips when developing a react website

### Navigation Tips
* use through history `let {history} = this.props; history.isActive('home') // true or false`
* use `<Link>` tag with `<Link activeClass={path}>` attribute
* to ignore or url params or query params, enclose path attribute in your Route like this
  <br /> ex: `<Route path="archives(/:article)" name="archives" component={Archives}></Route>`

