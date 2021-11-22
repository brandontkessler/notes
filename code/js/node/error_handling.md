# Error Handling

In api/controllers/errors.js

```js
const notFound = (req, res, next) => {
  const error = new Error('Not found');
  error.status = 404;
  next(error)
}

const errorHandler = (err, req, res, next) => {
  return res.status(err.status || 500).json({
    error: {
      message: err.message || "Problems"
    }
  })
}

module.exports = {
  notFound,
  errorHandler
};
```

Then in app.js, add at the end of other route calls:

```js
const errors = require('./api/controllers/errors');

// Other Routes here:

app.use(errors.notFound);
app.use(errors.errorHandler);

// app.listen...
```

Now if it doesn't trigger one of the routes called, it will trigger the not found (404) route and then get handled finally through the error handler that will throw a 500 error by default if not receiving an error code before.
