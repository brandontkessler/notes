# React Notes

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
    class Pet extends React.Component {
      render() {
        const liStyle = {fontSize: '1.5em'}
        return (
          <div className="container">
            <h2>Javi</h2>
            <img src="some_url_to_png",
                 alt="some alt text" />
            <h5 style={{fontSize: '2em', margin: '2px'}}>List Header</h5>
            <ul>
              <li style={liStyle}>item 1</li>
              <li style={liStyle}>item 2</li>
            </ul>
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
