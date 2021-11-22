# Logging

This is used with Morgan as a dev dependency.

```js
const morgan = require('morgan');

app.use(morgan('dev'));
```

Include the app.use prior to routes being triggered. When routes are triggered, they will be automatically logged to the console.
