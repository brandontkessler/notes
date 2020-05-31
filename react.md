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
      title="pasta"
      ingredients={["flour", "water", "sauce"]}
      img="spaghetti.jpg" // this file should be placed in public folder
      instructions="Mix ingredients"
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

**PropTypes**:

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

