## What does React do?
At its very core, React basically maintains a tree for you. This tree is able to do efficient diff computations on the nodes.  
React allows you to effectively re-construct your DOM in JavaScript and push only those changes to the DOM which have actually occurred.

### **JSX**
---
JSX is simply syntactic sugar for creating very specific JavaScript objects.

JSX
```js
const tag = <h1>Hello</h1>
```
Translate to JS
```js
const tag = React.createElement("h1", {}, "Hello")
```
React.createElement creates a plain old JavaScript object. In fact, you can manually call it and see for yourself!   
the above will generate following object
```js
{
    $$typeof: Symbol(react.element),
    key: null,
    props: {children: "Hello"},
    ref: null,
    type: "div"
}
```

### **renderer**
---
```js
ReactDOM.render(<App />, container)
```
ReactDOM in turn, recursively creates nodes depending on their 'type' property and appends them finally to the DOM.

### **Reconciliation**

React, in fact does very lazy reconciliation. React would make the least amount of changes possible   
heavy tree diffing, which are much expensive algorithms than the heuristic     O(n) react follows for diffing). So, React decides to destroy all children and re-create the children from scratch.

### **keys**
---
When adding/removing elements in a node, React would simply loop over the children in old tree and children in the new tree of the node and mark the places where it needs to perform any addition/removal.   
with key implementaion 
```js
<ul>
    <li key="A">A</li>
    <li key="B">B</li>
</ul>
```
Now, if this gets changed to:
```js
<ul>
    <li key="Z">Z</li>
    <li key="A">A</li>
    <li key="B">B</li>
</ul>
```
React knows we only need to add key='Z'.