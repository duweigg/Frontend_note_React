# React-Redux
## **Redux**
### **Components**
---
it’s like a global object which holds information that you use for various purposes later in the app    
There are no rules as to which data should be kept in Redux, so, it’s all up to you. However, bear in mind to always store only serializable data.   
**One pattern that Redux follows is called “Single Source Of Truth”**, which means that we have only one place (called Store) where we store the only state for the whole application.   
In other words, one app — one store — one state.   
However, components in React or other frameworks are free to contain their own internal state as well. Normally, you wouldn’t want to put literally everything in the application state.   
**Another pattern that Redux follows is called “immutability”**. 
In short, immutability means that we don’t change the state object and its properties directly. Instead, we make a new object, recalculate the new application state and update it with our newly created object. We want to leave our old state object intact.   
* Actions   
    Actions are plain JavaScript objects that describe WHAT happened, but don’t describe HOW the app state changes.   
    dispatch (send) them to our store instance, The rest is handled by the reducers.   
    Redux requires our action objects to contain a type field. This field is used to describe what kind of action we are dispatching and it should usually be a constant that you export from a file.
    All other fields in the action object are optional and are up to you.
    ```jsx
    { type: ADD_NOTE, title: 'Some Title', content: 'This is an action object' }
    ```
    **Action Creators**.   
    They are basically functions that generate and return plain JavaScript objects. They are used so that we can “insert” dynamic data in our actions
    ```JSX
    function addNote(title, content) {
        return { type: ADD_NOTE, title: title, content: content };
    }
    ```
* Reducers   
    Reducers are pure functions that define HOW the app state changes.   
    The reducer function takes two parameters: the previous app state, the action being dispatched and returns the new app state.   
    ```jsx
    (previousState, action) => newState
    ```
    In a real-world application, your reducers most probably will get very complex. To deal with reducer complexity, we chunk them down in multiple, simpler reducers and later, we combine them with a Redux helper function called ```combineReducers```.   
    The main reducer is conventionally called “Root Reducer”.   
* Store   
    the store hold the state of the application.
    The store is actually an object, It contains a few extra things other than your application’s state as well.     
    The state in Redux is in the form of a JavaScript Object and is often referred to as the “state tree”. You can put whatever values you want to store in it and you can nest them as much as you need.

### **Data Flow**
---
Data Flow Diagram

![Data Flow](https://miro.medium.com/max/700/1*BZQ8FPvJWhRBXOEd3n9Yhw.png)

### **Setting up**
---
suggested set up of directory list   
![directory List](https://miro.medium.com/max/239/1*Eec7SVfNpcMSOKo6yUNhIQ.png)

### **Subscribe** (good to know)
---
the store in Redux is an object. It has a function called subscribe, which we can use for subscribing to changes made to the state tree.   
```jsx
store.subscribe(() => {
  renderNotes(); // any render function
});
```
Note: we’re going to simply re-render all the Notes in our unordered list.
Conversely, if you’re using a framework / library like React, Angular or Vue, there’s probably going to be some optimized diffing algorithm that will calculate what needs to be updated and save you the trouble of having to do this on your own or even completely re-rendering stuff in your DOM, like we do.

### **Unsubscribing from the store** (good to know)
---
If you want for some reason to unsubscribe from the store, the store.subscribe function returns a function. Thus, by calling that function we unsubscribe from the store:
```jsx
const unsubscribe = store.subscribe(() => {
  renderNotes();
});

unsubscribe();
// Adding new notes won’t trigger a change in the UI now
```

### **Initializing State**
---
Before we get into initializing our app state, it would be wise to take a second look at reducers and really understand what’s going on:  
1) Each reducer has its own state, which might be different from the app state (e.g., you might have more than one reducer).
Now, because we only have one reducer (the root reducer), it ends up being the state of our app.
2) When Redux initializes our app, it sends an action that won’t satisfy any of our cases in the switch statement.
In fact, if we log the action type it in the console, this is what we’ll get the first time we visit the page:
    ```jsx 
    {type: "@@redux/INITp.n.3.p.e.b"}
    ```
    Because we didn’t supply an initial state of the app, the state is undefined, so it defaults back to our initialState object.
3) Your apps will most likely have more than one reducer. Redux provides a function called ```combineReducers``` that lets us combine multiple reducers and pass them down to the createStore function as if they were a single reducer.   
    ```js
    import notesReducer from './notesReducer';
    import visibilityFilter from './visibilityFilter';
    import { combineReducers } from 'redux';

    const reducers = combineReducers({
    notes: notesReducer,
    visibility: visibilityFilter,
    });

    export default reducers;
    ```
4) As the second parameter of the createStore function, we can supply the initial state of the application. This will overwrite any default values we have in our reducers.

Connecting our app with Redux DevTools browser extensions   
add this line as the third parameter of the createStore function:
```
window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
```
This is how your store.js file should look like now:
```js
import { createStore } from 'redux';
import reducers from '../reducers/reducers';

let initialState = {
  notes: [
    { title: 'You are awesome', content: 'No, wait, I meant legendary!' },
    { title: 'Ooops', content: 'I was talking to myself' },
  ],
  visibility: 'AWESOME_TAG',
};

export default createStore(
  reducers,
  initialState,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);
```

## **React-Redux**
1) React-Redux provides a React component called Provider, which makes our application store available throughout our entire application.
This is done by surrounding the App with the Provider component in our index.js file and passing the store as a property to the Provider component.   
    ```js
    import { Provider } from 'react-redux';
    import store from './redux/store/store';

    ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
    );
    ```
2) React Redux provides a connect function which we use for class-based components only. For functional components, however, we use hooks provided by the React Redux package.   
### **React-Redux Class Based Component (connector)**
---
* We use the connect function whenever we want to “connect” a component to Redux   
* The connect function is actually a Higher Order Component. It returns a function to which we supply the class name of our component   
* It also takes in two optional parameters: ```mapStateToProps``` and ```mapDispatchToProps```.   

**mapStateToProps**   
* mapStateToProps is actually a function which takes the entire state of our app as its first parameter and returns an object of data that our component will need   
* This function takes an optional second parameter, which lets you use some of the component props. 
    ```
    (state, ownProps?) => stateProps
    ```
    mapStateToProps is called every time our app state changes  
    our component will re-render when either ownProps or stateProps is different


```js

import { removeNote } from '../redux/actions/actions';
  render() {
    const notesItems = this.props.notes.map((note, index) => (
      <li key={index}>
        <b>{note.title}</b>
        <button onClick={() => this.removeNote(index)}>x</button>
        <br />
        <span>{note.content}</span>
      </li>
    ));
XXXXXXXXXXXXX
const mapStateToProps = (state) => {
  return {
    notes: state.notes,
  };
};

const mapDispatchToProps = {
  removeNote: removeNote,
};

export default connect(mapStateToProps, mapDispatchToProps)(AllNotes);
```

**mapDispatchToProps**   
* This parameter is used for mapping (injecting) action creators to the component’s props
*  mapDispatchToProps can actually be either an object or a function. The official React Redux documentation recommends it to be an object
```js
import { addNote } from '../redux/actions/actions';
XXXXXXXXXXX
    this.props.addNote(title, content);
XXXXXXXXXXX
export default connect(null, {
  addNote: addNote,
})(NotesForm);
```

### **React-Redux for functional components**
---
useSelector is a replacement (roughly speaking) for the connect function’s mapStateToProps parameter.The same accepts two parameters — a selector function and an equality function.   

**selector**   

The selector function’s purpose is to return a portion of the store. It does this with the help of its only parameter, the application’s store.
```js
const notes = useSelector((store) => store.notes);
```