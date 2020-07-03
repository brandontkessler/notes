# Database Setup for Node

## MongoDB

Use mongo cloud atlas and create a free cluster. Under the Network Access tab, go to IP Whitelist, click add IP Address, click Add Current IP Address, and click confirm. Then go back to the cluster, click connect and connect your application to get the URL for any driver/version available, in this case, Node.js 3.6 or later. Add the url to environment vars to be used in app.

### Connection

In app.js connect to the database. Adding the routes that are used in the below example as well:

```js
const mongoose = require('mongoose');
const authRoutes = require("./api/routes/auth");

mongoose.connect(process.env.DB_URL, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  useCreateIndex: true,
  autoIndex: true,
})

app.use("/api/auth", authRoutes);
```

### Models

Auth example using a pre hook and a method:

```js
const mongoose = require("mongoose");
const bcrypt = require("bcrypt");

const userSchema = new mongoose.Schema({
  email: {
    type: String,
    required: true,
    unique: true,
  },
  username: {
    type: String,
    required: true,
    unique: true
  },
  password: {
    type: String,
    required: true
  },
  profileImageUrl: {
    type: String
  }
});

userSchema.pre("save", async function(next) {
  try {
    if (!this.isModified("password")) {
      return next()
    }
    let hashed = await bcrypt.hash(this.password, 10);
    this.password = hashed
    return next();
  } catch(err) {
    return next(err);
  }
})

userSchema.methods.comparePassword = async (candidatePassword, next) => {
  try {
    let isMatch = await bcrypt.compare(candidatePassword, this.password);
    return isMatch;
  } catch (error) {
    return next(error)
  }
}

module.exports = mongoose.model("User", userSchema);
```

### Controller Usage

Using the model created above, we can then use it in our controllers.

```js
const User = require('../models/user');
const jwt = require('jsonwebtoken');

exports.signup = async function(req, res, next) {
  try {
    const user = await User.create(req.body);
    const { id, username, profileImageUrl } = user;
    const token = jwt.sign(
      {
        username,
        profileImageUrl
      }, process.env.SECRET_KEY
    );

    return res.status(200).json({
      id,
      username,
      profileImageUrl,
      token
    })
  } catch(err) {
    err.message = 'Auth failed'
    return next({
      status: 400,
      message: err.message
    })
  }
}
```

### Router Usage

Then we just need to use them as routes.

```js
const express = require("express");
const router = express.Router();
const { signup, signin } = require("../handlers/auth");

router.post("/signup", signup)
router.post("/signing", signin)

module.exports = router;

```
