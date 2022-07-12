# React - Notes



- [React - Notes](#react---notes)
  - [Intro](#intro)
    - [Example 2](#example-2)
    - [Example 3](#example-3)
  - [Components](#components)
    - [Functional Components](#functional-components)
    - [Class Components](#class-components)
  - [State](#state)
  - [Hooks](#hooks)
    - [Example 5 Counter App using Hooks](#example-5-counter-app-using-hooks)
  - [Lifecycle Methods](#lifecycle-methods)
  - [Lists](#lists)
  - [Sharing state](#sharing-state)
    - [Final example](#final-example)












## Intro

this.props contained a: 1 and b: 2

```js
<Modal {...this.props} 
    title='Modal heading' 
    animation={false}
>
```

SAME AS

```js
<Modal 
    a={this.props.a} 
    b={this.props.b} 
    title='Modal heading' 
    animation={false}
>
```
### Example 2

```js
var parts = ['two', 'three'];
var numbers = ['one', ...parts, 'four', 'five']; 
// ["one", "two", "three", "four", "five"]
```

### Example 3

Just assume we have an object like this:
```js
var person= {
    name: 'Alex',
    age: 35 
}


<Modal 
    {...person} 
    title='Modal heading' 
    animation={false} />

<Modal 
    name={person.name} 
    age={person.age} 
    title='Modal heading' 
    animation={false} 
/>
```

```bash
npx create-react-app first-react-app
```

public
contains files related to how the application will display on the client
src
contains files that will be compiled into a bundle file and injected into index.html


Bundling
It uses what is called a "file loader".( In the case of Create React App, Webpack is used.)
Webpack creates a "bundle" file containing the content of multiple files that need to be "bundled" together and it is all added together into a single file. Instead of making the HTML file go and find multiple files, which can slow down load times tremendously, it only has to find one file.
all CSS and JS files need to be added to the src folder, otherwise webpack won't see them

SRC
 index.js
This file is the entry point into our application. In our code, a method called ```ReactDOM.render()```
is used to find an element with id="root" in the HTML and add our React application inside of that element (similar to the previous lesson).
App.js
This file is the main component that will be rendered to the DOM, which currently includes the React logo image and the default text, that we see in the output.

Example index.js

```js
ReactDOM.render(
            <h1>Hello, React!</h1>,
             document.getElementById('root')
);
```

Example 2
```js
const name = "David";
const el = <p>Hello, {name}</p>;

const name = "myClass";
const el = <div className={name}></p>;

const name = "myName";
<div id={name}></div> 
```

Example 3  (DONT USE IN PRACTICE)

```js
let counter = 0;

function show() {
  counter++;
  const el = <p>{counter}</p>;
  ReactDOM.render(
    el, document.getElementById('root')
  );
}
setInterval(show, 1000);
```

## Components

### Functional Components
Notice that the name of the functional component begins with a capital letter 

```js
function Hello() {
  return <h1>Hello world.</h1>;
}
const el = <Hello />; 

ReactDOM.render(
  el, 
  document.getElementById('root')
);
```

Functional components can accept arguments: props
Props are read-only and cannot be modified.

```js
function Hello(props) {
  return <p>Hello, {props.name}!</p>;
}
```

Now, we can add a name attribute to our element

```js
const el = <Hello name="David" />;
```



### Class Components
1. All class components need to extend the ```React.Component``` class
2. We can rewrite our Hello functional component as a class component
3. Class components need to have a render method

```js
class Hello extends React.Component {
  render() {
    return <h1>Hello world.</h1>;
  }
} 
```

Props in Class Components  ->  this.props

```js
class Hello extends React.Component {
  render() {
    return <p>Hello, {this.props.name}!</p>;
  }
} 
```

Example 4 smaller components

```js
function App() {
  return <div>
    <Hello name="David" />
    <Hello name="James" />
    <Hello name="Amy" />
  </div>;
}
```



## State

Many web apps need their components to change their data, for example, after user interaction
However, props cannot be changed.
State is an object that is added as a property in class components.

```js
class Hello extends React.Component {
  state = {
    name: "James"
  }

  render() {
    return <h1>Hello {this.state.name}.</h1>;
  }
}
```

state is just a simple object, that contains key:value pairs
State should not be modified directly
React provides a setState() method, that can be used to modify state.

```js
this.setState({  name: "James", age: 25 }); 
```

when setState is called, React automatically re-renders the affected component with the new state!

Example 5 Counter App

```js
class Counter extends React.Component {
  state = {
    counter: 0
  }
  increment = () => {
    this.setState({
     counter: this.state.counter+1});
  }
  render() {
    return <div>
    <p>{this.state.counter}</p>
    <button onClick={this.increment}>Increment</button>
    </div>;
  }
}
```

## Hooks

allowing to use state inside of functional components

we need to import the useState hook:
```js
import React, { useState } from 'react'; 

function Hello() {
  const [name, setName] = useState("David");

  return <h1>Hello {name}.</h1>;
}
```

    name = state variable
    setName  =  function that allows to change the state

we pass "David" as the initial value for our name variable to useState().

### Example 5 Counter App using Hooks

```js
function Counter() {
  const [counter, setCounter] = useState(0);

  function increment() {
    setCounter(counter+1);
  }

  return <div>
  <p>{counter}</p>
  <button onClick={increment}>
    Increment
  </button>
  </div>;
} 
```


## Lifecycle Methods

Mounting is the process when a component is rendered on the page
Unmounting is the process when a component is removed from the page
componentDidMount method is called when a component is rendered on the page

```js
componentDidMount() {
  this.setState({counter: 42});
}
```

componentDidMount is typically used for populating the state inside of a component when it initially mounts to the DOM

componentWillUnmount lifecycle method is called right before the component is removed from the DOM. It can be used to free up resources taken by the component

componentDidUpdate

```js
componentDidUpdate() {
  alert("Number of clicks: " + this.state.counter);
}
```

useEffect Hook

The lifecycle methods we covered are only available for class components.
However, React provides a special Hook called useEffect to make lifecycle methods available in functional components

```js
function Counter() {
  const [counter, setCounter] = useState(0);

  useEffect(() => {
    alert("Number of clicks: " + counter);
  });

  function increment() {
    setCounter(counter+1);
  }
  return <div>
  <p>{counter}</p>
  <button onClick={increment}>Increment</button>
  </div>;
}
```

useEffect runs both, after the first render and after every update

To mimic componentWillMount:

```js
import React, { useState, useEffect } from 'react';


useEffect(() => {
  //do something
}, [counter] );  
```

To mimic componentWillUnmount:

```js
useEffect(() => {
  // do something
  
  return () => {
    // cleanup
  }; 
});
```

Event Handling

use camelCase

```js
<button onClick={handleClick}>
  My Button
</button> 
```

Handling User Input:  onChange event 

```js
function Converter() {
  const [km, setKm] = useState(0);

  function handleChange(e) {
    setKm(e.target.value);
  }
  function convert(km) {
    return (km/1.609).toFixed(2);
  }

  return <div>
  <input type="text" value={km}
     onChange={handleChange} />
  <p> {km} km is {convert(km)} miles </p>
  </div>;
}
```

The value of the text field is accessed via the e object, which represents the React event. It is passed to the event handler function as an argument and can be used to access the event object.

```js
function AddForm() {
  const [sum, setSum] = useState(0);
  const [num, setNum] = useState(0);

  function handleChange(e) {
    setNum(e.target.value);
  }

  function handleSubmit(e) {
    setSum(sum + Number(num));
    e.preventDefault();
  }

  return <form onSubmit={handleSubmit}>
  <input type="number" value={num} onChange={handleChange} />
  <input type="submit" value="Add" />
  <p> Sum is {sum} </p>
  </form>;
}
```

Notice the ```e.preventDefault();``` statement. This statement prevents the default behavior of the form, which, by default, reloads the page when submitted. In JavaScript we would use return ```false;``` for that, but in React we need to call ```preventDefault()```.

## Lists
We can define a MyList component and pass it the array as a prop using a custom data attribute:
```js
const arr = ["A", "B", "C"];
<MyList data={arr} />

function MyList(props) {
  const arr = props.data;
  const listItems = arr.map((val) =>
    <li>{val}</li>
  );
  return <ul>{listItems}</ul>;
}
```

Each element in a list must have a key attribute.

```js
const listItems = arr.map((val, index) =>
  <li key={index}>{val}</li>
);
```


## Sharing state

```js
function ContactManager(props) {
  const [contacts, setContacts] = useState(props.data);

  return (
    <div>
      <AddPersonForm />
      <PeopleList data={contacts} />
    </div>
  );
} 
```

We also clear the value of the text field using setPerson('') after adding a new person.



### Final example

```js
function AddPersonForm(props) {
  const [ person, setPerson ] = useState('');
    
  function handleChange(e) {
    setPerson(e.target.value);
  }
    
  function handleSubmit(e) {
    if(person !== '') {
      props.handleSubmit(person);
      setPerson('');
    }
    e.preventDefault();
  }
  return (
    <form onSubmit={handleSubmit}>
      <input type="text" 
        placeholder="Add new contact" 
        onChange={handleChange} 
        value={person} />
      <button type="submit">Add</button>
    </form>
  );
}


function PeopleList(props) {
  const arr = props.data;
  const listItems = arr.map((val, index) =>
    <li key={index}>{val}</li>
  );
  return <ul>{listItems}</ul>;
}

function ContactManager(props) {
  const [contacts, setContacts] = useState(props.data);

  function addPerson(name) {
    setContacts([...contacts, name]);
  }

  return (
    <div>
      <AddPersonForm handleSubmit={addPerson} />
      <PeopleList data={contacts} />
    </div>
  );
}
const contacts = ["James Smith", "Thomas Anderson", "Bruce Wayne"];

ReactDOM.render(
  <ContactManager data={contacts} />, 
  document.getElementById('root')
);

```