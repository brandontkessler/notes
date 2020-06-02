# React Notes

## Basics

Babel transpires JSX into Vanilla JS

We can add react and jsx to an html file by adding it's cdn in the head:

```html
<head>
  <link rel="stylesheet" href="style.css">
  <script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.26.0/babel.js"></script>
</head>
<body>
  <div id="app"></div>
  <script type="text/babel">

    class ItemList extends React.Component {
      render() {
        const items = ["apple", "pencil", "cup"]
        const liStyle = {fontSize: '1.5em'}
        return (
          <ul>
            {items.map((item, i) => {
              return <li key={i} style={style}>{item}</li>
            })}
          </ul>
        )
      }
    }

    class Pet extends React.Component {
      render() {
        return (
          <div className="container">
            <h2>Javi</h2>
            <img src="some_url_to_png"
                 alt="some alt text" />
            <h5 style={{fontSize: '2em', margin: '2px'}}>List Header</h5>
            <ItemList />
          </div>
        );
      }
    }
    ReactDOM.render(<Pet />, document.getElementById("app"));
  </script>
</body>
```

* The react cdns are [here](https://reactjs.org/docs/cdn-links.html)
* The babel cdn are [here](https://cdnjs.com/libraries/babel-standalone)
* For Babel, we have to use className instead of claa inside the html tags
* Style can be added using a linked stylesheet
* Inline styles in React are acceptable
  * Can be done directly like the h5 tag using double curly brackets
  * Can be done creating an object like liStyle above
* Curly braces allow us to evaluate an object, that's why `style={liStyle}` is necessary as it allows us to evaluate the liStyle object
  * Similarly, we can insert {5 + 5} and it will evaluate on the page
* We can render using map on a vector, when doing so we need to provide a key which can be done with just placing an index in there.
  * As shown in `items.map()`
* We can create multiple react components and render one within another like how we used the ItemList within the Pet class.

---

## Create React App

Webpack - a module bundler

* Combines different JS files into a bundle.js
* Has a plugin system to run tools like babel
* Also bundles other assets like css and images
* Configurable

create-react-app is a command that uses webpack to build a starting point for a react application

```bash
npx create-react-app <name of app>
```

After creating the app, cd into it and run it.

For example:

```bash
npx create-react-app client
cd client
npm start
```

### Components in CRA

Components are added in the src folder as js files with a capitalization by convention. For example, adding a pet component would be creating a file in src called Pet.js with the following:

```js
import React, { Component } from 'react';

class Pet extends React.Component {
  render() {
    return (
      <div className="container">
        <h2>Javi</h2>
        <img src="some_url_to_png"
             alt="some alt text" />
        <h5 style={{fontSize: '2em', margin: '2px'}}>List Header</h5>
        <ItemList />
      </div>
    );
  }
}

export default Pet;
```

Then in index.js, we don't have to import App as we're not using that component anymore and instead are using Pet:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.ss';
import Pet from './Pet';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(
  <React.StrictMode>
    <Pet />
  </React.StrictMode>,
  document.getElementById('root')
)

serviceWorker.unregister();
```

### Props

Definition: immutable data passed to components

* accessible via `this.props` ie. `{this.props.text}`
* NEVER change this.props in your component:

```js
class ShowText extends Component {
  render() {
    this.props.text = "something" // TypeError
    this.props = {} // Never do this!
    this.props.newProp // don't do this. Use default props instead

    return <div>{this.props.text}</div>
  }
}
```

Instead, props should be included where the component is being called, generally in App.js file:

```js
import React from 'react';
import Recipe from './Recipe';
import Navbar from './Navbar';

function App() {
  return (
    <Navbar />
    <Recipe
      title="pasta"
      ingredients={["flour", "water", "sauce"]}
      img="spaghetti.jpg" // this file should be placed in public folder
      instructions="Mix ingredients"
    />
    <Recipe
      title="lasagna"
      ingredients={["broccoli", "water", "sauce", "noodles]}
      img="noddle.jpg" // this file should be placed in public folder
      instructions="Bake"
    />
  )
}

/* Instead of having multiple recipes like above, we should
make a RecipeList component that these would live under. Then
just import the RecipeList instead of the recipes.
*/
```

It is accessible in the component itself:

```js
import React, { Component } from 'react';

class Recipe extends Component {
  static defaultProps = {
    ingredients = ["chicken", "water"]
  }

  render() {
    const { title, img, instructions } = this.props;
    const ingredients = this.props.ingredients.map((ing, index) => (
      // using parentheses instead of curly brackets relieves the necessity to return anything. This can be used for one liner functions that return that line.
      <li key={index}>{ing}</li>
    ))

    return (
      <div className="recipe-card">
        <div className="recipe-card-img">
          <img src={img} alt="fun img" />
        </div>
        <div className="recipe-card-content">
          <h3 className="recipe-title">Recipe { title }</h3>
          <h4>Ingredients:</h4>
          <ul>
            {ingredients}
          </ul>
          <h4>Instructions:</h4>
          <p>{instructions}</p>
        </div>
      </div>
    )
  }
}

export default Recipe;
```

* staticDefault props allow us to set the default values

### Prop Types

Type checker for props, only works in development.

`> npm install prop-types`

```js
import PropTypes from 'prop-types';

class Ingredients extends Component {
  static propTypes = {
    ingredients: PropTypes.arrayOf(PropTypes.string)
                          .isRequired
  }

  render() {
    // Rest of code
  }
}
```

Helps with catching bugs as this works only in development and doesn't do anything in production.

### Prop Children

A collection of children inside the component.

```js
class Row extends Component {
  render() {
    return(
      <div style={{
        display: 'flex',
        flexDirection: 'row',
        justifyContent: 'space-around',
      }}>
        {this.props.children}
      </div>
    )
  }
}
```

Now, we can wrap the Row component around any html tag:

```js
function App() {
  return (
    <Row>
      <p>dog</p>
      <div>Lonzo</div>
      <h1>Javi</h1>
    </Row>
  )
}
```

In this case, this.props.children refers to the p, div, and h1 tags.

#### Looping over Children

Children can be looped over with React.Children.map and React.Children.forEach.

```js
class IgnoreFirstChild extends Component {
  render() {
    return (
      <div>
        {React.Children.map(children, (child, i) => {
          if (i < 1) return
          return child
        })}
      </div>
    )
  }
}
```

Then, `<IgnoreFirstChild />` component maps all children and ignores the first:

```js
<IgnoreFirstChild>
  <h1>First</h1>
  <h2>Second</h2>
  {() => <h2>Third</h2>}
</IgnoreFirstChild>
```

This is better than just doing this.props.children.map because it can handle things like if the child was a function like the third child above.

#### Counting Children

This can be done with React.Children.count(this.props.children). This is better than this.props.children.length because it can handle differences in type like functions, strings, etc.

#### Converting children to array

Can be done with React.Children.toArray(this.props.children). Useful for things like sorting.

```js
class Sort extends Component {
  render() {
    const children = React.Children.toArray(this.props.children)
    return <p>{children.sort().join(" ")}</p>
  }
}
```

##### Enforcing only a Single Child

```js
class Executioner extends Component {
  render() {
    func = React.Children.only(this.props.children)
    return func()
  }
}
```

The above returns the ONLY child in this.props.children and will break if there are more than one. The child is a function and then we return the execution of that function above.

##### Changing Children Props

Using an example of a RadioGroup with RadioButtons as children like:

```js
render() {
  return (
    <RadioGroup>
      <RadioButton value="first">first</RadioButton>
      <RadioButton value="second">second</RadioButton>
      <RadioButton value="third">third</RadioButton>
    </RadioGroup>
  )
}
```

We want to provide a name property to each radio button so that they all have the name "g1" as a property.

We can do this by adding a method to the parent group called "renderChildren()" and call that in the return statement. We map over each child, clone the element, then update its properties with an object.

```js
class RadioGroup extends Component {
  renderChildren() {
    return React.Children.map(this.props.children, child => {
      return React.cloneElement(child, {
        name: this.props.name
      })
    })
  }

  render() {
    return (
      <div className="group">
        // {this.props.children} <- not needed anymore
        {this.renderChildren()}
      </div>
    )
  }
}
```

Then we can pass the property, name, from the parent to its children.

```js
render() {
  return (
    <RadioGroup name="g1">
      <RadioButton value="first">first</RadioButton>
      <RadioButton value="second">second</RadioButton>
      <RadioButton value="third">third</RadioButton>
    </RadioGroup>
  )
}
```

### State

Date in application that can change where props cannot.

Example:

```js
class App extends Component {
  constructor(props) {
    super(props);
    this.state = { favColor: 'red' }

    setTimeout(() => {
      this.setState({favColor: 'blue'})
    }, 3000);
  }

  render(){
    return (
      <div>
        My favorite color:
        {this.state.favColor}
      </div>
    )
  }
}
```

The constructor and super(props) portion is boilerplate for all react.

Never modify state directly. Always call setState to make any change.

* State is always passed from a parent down to a child component as a prop.
* State should not be passed to a sibling or a parent.

Bad practice: Never duplicate state. Never assign a prop to state.

```js
class Item extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name: this.props.name,
      hobbies: this.props.hobbies
    }
  }
}

// This is bad!
```

#### Stateless Functional Components

Can be used when there is no state passed down.

```js
import React from 'react';

const Greeting = props => (
  <h1>Hello, {props.name}</h1>
)

export default Greeting;
```

No component is used so is not imported from react. Instead, just a stateless function is used.

It's good practice and provides some performance benefits to using a stateless function instead of a class when there is no state required.

### Thinking in React

[article](https://reactjs.org/docs/thinking-in-react.html)

#### Step 1: Break the UI into componenet Heirarchy

Start by drawing boxes around every component and subcomponent and give them names (can be done on pen and paper) and place them in a heirarchy. This may look like:

* FilterableProductTable
  * SearchBar
  * ProductTable
    * ProductCategoryRow
    * ProductRow

#### Step 2: Build a static version in react

Build it with seed data that matches the data model from the API.

Build components that reuse other components and pass data using props. Don't use state at all to build static version as state is reserved for interactivity.

The component at the top of the hierarchy will take the data model as a prop.

#### Step 3: Identify the Minimal (but Complete) Representation of UI State

For example, when building a todo list, don't keep a separate state variable that counts the length of the list, instead take the length of the todo items array.

Three questions to determine state:

1. Is it passed in from a parent via props? If so, not state.
2. Does it remain unchanged over time? If so, not state.
3. Can you compute it based on any other state or props in your component? If so, not state.

Then, identify which component mutates, or owns, the state. For each piece of state:

* Identify every component that renders something based on that state.
* Find a common owner component (a single component above all others).
* Either the common owner or another higher up in the hierarchy should own the state.
* If none make sense, create a new component solely for holding the state and add it somewhere in the hierarchy above the common owner component.


