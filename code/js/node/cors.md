# Cross-Origin Resource Sharing

Allow CORS by adding the appropriate headers to response objects which can be done with middleware prior to the routes in app.js.

* REST APIs allow access to any client which is why the first header provides access.
* The second header is saying which headers the api will accept.
* The third checks for OPTIONS which always gets sent by the brwser on a post or put request.

```JavaScript
app.use((req, res, next) => {
	res.header('Access-Control-Allow-Origin', '*');
	res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept, Authorization');
	if (req.method === 'OPTIONS') {
	res.header('Access-Control-Allow-Methods', 'PUT, POST, PATCH, DELETE, GET');
	return res.status(200).json({});
}
	next();
})
```

This can be simplified using the cors package which was installed and used as a middleware in app.js:

```js
// app.js file
const cors = require('cors');

app.use(cors());

// Routes
```
