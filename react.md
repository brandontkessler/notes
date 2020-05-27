Babel transpires JSX into Vanilla JS

We can add react and jsx to an html file by adding it's cdn in the head:

```
<head>
    <script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.26.0/babel.js"></script>
</head>
```

* The react cdns are [here](https://reactjs.org/docs/cdn-links.html)
* The babel cdn are [here](https://cdnjs.com/libraries/babel-standalone)

