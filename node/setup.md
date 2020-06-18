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
    handlers/
    middleware/
    models/
    routes/
  app.js
  nodemon.json
  package.json
  