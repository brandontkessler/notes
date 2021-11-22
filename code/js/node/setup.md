# Setup

After initializing node, the packages needed are express, cors, mongoose, bcrypt, jsonwebtoken. Dev packages are nodemon, morgan.

Update the package.json file to include scripts:

```json
{
	"scripts": {
		"start": "node app.js",
		"dev": "nodemon app.js"
	}
}
```

## Structure

root/
  api/
    controllers/
    middleware/
    models/
    routes/
  app.js
  nodemon.json
  package.json
  
## App.js

```js
const express = require('express');
const app = express();
const morgan = require('morgan');

const port = process.env.PORT;

app.use(express.urlencoded({ extended: false }));
app.use(morgan('dev'));

app.listen(port, () => {
  console.log(`Server running on port ${port}`)
})
```
