# React-Router
## Routers
### **BrowserRouter, HashRouter**
---
BrowserRouter is used for applications which have a dynamic server that knows how to handle any type of URL 
HashRouter is used for static websites with a server that only responds to requests for files that it knows about.

any router expects to receive only one child. Take the example below
```JSX
ReactDOM.render(
  <BrowserRouter>
    <App/>
  </BrowserRouter>,
  document.getElementById(‘root’));
```
n this example, the <App/> component is the child to the <BrowserRouter> and should be the only child. Now, the routing can happen anywhere within the <App/> component, however, it is considered good practice to group and place all the routes in the same place. More on this later.

### **History**
---
Each router creates a history object that it uses to keep track of the current location and re-renders the application whenever this location changes.   
For this reason, the other React Router components rely on this history object being present; which is why they need to be rendered inside a router.   
The history object created by the Router contains a number of properties and one of the location property whose value is also an object.   
The location object within the history object is shaped like so   
```{ pathname, search, hash, state }```   
The location object properties are derived from the application URL.

### **Routers**
---
The path is a prop on the <Route/> component that describes the pathname that the route should match as shown in the example that follows   
```JSX
<Route path=”/items”/>
```
This route is matched when the pathname start with /items/ for example /items/2.    
If the intention is to strictly match only /items, the <Route/> component accepts an exact prop. 
```JSX
<Route exact path=”/items” />
```
When a path is matched, a React component should be rendered so that there’s a change in the UI.
the path also supports RegExp

**properties**
* Component   
    The component prop defines the React element that will be returned by the Route when the path is matched. This React element is created from the provided component using ```React.createElement```. Below is an example using the component prop.   
    ```JSX
    <Route 
        exact 
        path=”/items” 
        component={Items}
    />
    ```
    In this example, the Items component will be returned when the path matches the current location.
* render   
    The render prop provides the ability for inline rendering and passing extra props to the element.
    ```JSX
    <Route 
        exact 
        path=”/items” 
        render={() => (<div>List of Items</div>)}
    />
    ```
    ```JSX
    const cat = {category: “food”}
    <Route 
        exact path=”/items” 
        render={props => <Items {…props} data={cat}/>}
    />
    ```
* Children   
   The element defined by the child prop is returned for all paths irrespective of whether the current location matches the path or not.
    ```JSX
    <Route children={props => <Items {…props}/>}/>
    ```
    In this case, Items component is always rendered.

### **Switch**
---
The react-router library also contains a <Switch/> component that is used to wrap multiple <Route/> components. The Switch component only picks the first matching route among all its children routes.
```JSX
<Switch>
  <Route 
    path=”/items” 
    render={() => (<div><em>List of items</em></div>)}
  />
  <Route 
    path=”/items/2" 
    render={() => (<div>Item with id of 2</div>)}
  />
</Switch>
```
when we navigate to /items/2. In the browser, only List of Items will be rendered. This is because the Switch component matches only the first path that matches the current location. In this example, the route /items was matched when /items/2 was entered in the browser’s address bar.

### **Link**
---
Link provides a way to navigates to other URL. 

The react-router package also contains a <Link/> component that is used to navigate the different parts of an application by way of hyperlinks. It is similar to HTML’s anchor element but the main difference is that **using the Link component does not reload the page but rather, changes the UI.** Using an anchor tag would require that the page is reloaded in order to load the new UI. When the Link component is clicked, it also updates the URL.   
  
The <Link/> component uses to as a prop to define the location to navigate to. This prop can either be a string or a location object. If it is a string, it is converted to a location object. Note that the pathname must be absolute.
```JSX
export const Home = () => (
  <div>
    Home Component
    <ul>
      <li>
        <Link to=”/items”>Items</Link>
      </li>
      <li>
        <Link to=”/category”>Category</Link>
       </li>
    </ul>
  </div>
);
```

### **Nested Routing**
---
When the router’s path and location are successfully matched, a **match** object is created. This object contains information about the URL and the path. This information can be accessed as properties on the match object.   
* url : A string that returns the matched part of the URL
* path : A string that returns the route’s path
* isExact : A boolean that returns true if the match was exact
* params : An object containing key-value pairs that were matched by the Path-To-RegExp package.
```JSX
export const Category = ({match}) => (
  <div>
  <h1>Category Component</h1>
  <h5>Click on a category</h5>
  <ul>
    <li>
      <Link to={`${match.url}/shoes`}>Shoes</Link>
    </li>
    <li>
      <Link to={`${match.url}/food`}>Food</Link>
    </li>
    <li>
      <Link to={`${match.url}/dresses`}>Dresses</Link>
    </li>
  </ul>
);
```
the point is **match.url** which is the current url {domain}/Category/  
When any one of these categories is clicked, the URL updates, however, there is no change in the UI.  
in order to fix this bug and ensure that the UI changes when a category link is clicked, we create a dynamic route within the Category component that uses match.path for its path prop and then dynamically change the UI.
```JSX
export const Category = ({match}) => (
  <div>
  <h1>Category Component</h1>
  <h5>Click on a category</h5>
  <ul>
    XXXXXXXX
  </ul>
  <Route
      path={`${match.path}/:categoryName`}
      render={props => (<div>{props.match.params.categoryName} category</div>)}
  />
);
```
Looking closely at the value of the path prop in the code snippet above, you can see that we use ```:categoryName```, a variable within the pathname.  
```:categoryName``` is the path parameter within the URL and it catches everything that comes after /category.  
Passing the value to the path prop in this way saves us from having to hardcode all the different category routes. Also, notice the use of template literals to construct the right path.  
A pathname like category/shoes creates a param object like the one below
```JSX
{
 categoryName: “shoes”
}
```

### **Protected Routes**
---
For this redirect to work as intended, the react-router package provides a <Redirect/> component to serve this purpose (redict to login when not authoriszed). This component has a to prop which is passed to it in form of an object containing the pathname and state as shown below.
```JSX
<Redirect 
  to={{pathname: ‘/login’, state: {from:props.location}}}
/>
```

### **Custom Routes**
---
Custom routes are a fancy way of saying nesting a route inside a component. This is typically done when there is a need to decide whether a component should be rendered, or not.  
In the case of a protected route, a given route should only be accessed when a user is logged in, otherwise, the user should be directed to the login page.   
App.js
```JSX
<Switch>
 <Route exact path="/" component={Home}/>
 <Route path="/items" component={Items}/>
 <Route path="/category" component={Category}/>
 <Route path="/login" component={Login}/>}/>
 <PrivateRoute 
   path="/admin" 
   component={Admin} 
   isAuthenticated={fakeAuth.isAuthenticated}
  />
</Switch>
```
PrivateRoute.js
```JSX
import React from 'react';
import { Route, Redirect } from "react-router-dom";

const PrivateRoute = ({component: Component, isAuthenticated, ...rest}) => (
  <Route {...rest} render={props => (
    isAuthenticated 
      ? 
      (<Component {...props}/>)
      :
      (<Redirect to={{pathname: '/login', state: {from: props.location}}}/>)
  )}/>
);
```
Login.js
```JSX
import React from 'react';
import {Redirect} from 'react-router-dom';

class Login extends React.Component {
  state = { 
    redirectToReferrer: false 
  };
  
  login = () => {
    fakeAuth.authenticate(() => {
      this.setState({
        redirectToReferrer: true
      })
    })
  };

  render() {
    const { from } = this.props.location.state || {from: {pathname: '/'}};
    const { redirectToReferrer } = this.state;
    
    if (redirectToReferrer) {
      return (
        <Redirect to={from}/>
      )
    }
    
    return (
      <div>
        <p> You must log in to view the content at {from.pathname} </p>
        <button onClick={this.login}> Log in </button>
      </div>
    )
  }
}

/* A fake authentication function */
export const fakeAuth = {
  isAuthenticated: false,
  authenticate(cb) {
    this.isAuthenticated = true;
    setTimeout(cb, 100)
  },
};

export default Login
```
