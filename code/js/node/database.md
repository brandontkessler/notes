# Database Setup for Node

## MongoDB

### Development

For development, use a local mongo database [reference](https://zellwk.com/blog/install-mongodb/):

```bash
> brew tap mongodb/brew
> brew install mongodb-community
>
> brew services run mongodb-community # starts database
> brew services stop mongodb-community
```

These are the basic commands to install via homebrew and to start and stop the mongo service. Enter the shell and create a new database to use then connect that database in development:

```bash
> mongo # enter shell
> use fake_db # create and switch to a db called fake_db
> exit # close the shell
```

The url to use with this for mongoose is (27017 is the default port for mongo):

```js
const url = 'mongodb://127.0.0.1:27017/fake_db'
```

### Production

Use mongo cloud atlas and create a free cluster. Under the Network Access tab, go to IP Whitelist, click add IP Address, click Add Current IP Address, and click confirm. Then go back to the cluster, click connect and connect your application to get the URL for any driver/version available, in this case, Node.js 3.6 or later. Add the url to environment vars to be used in app.

### Connection (app.js)

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

### Models (/api/models/user.js)

Auth example using a pre hook and a method (it also has a reference to a 'Message' model and stores a vector of IDs that are associated to each message this particular use writes):

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
  },
  messages: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'Message'
    }
  ]
});

userSchema.pre("save", async function(next) {
  try {
    if (!this.isModified("password")) return next()
    let hashed = await bcrypt.hash(this.password, 10);
    this.password = hashed
    return next();
  } catch(err) { return next(err) }
})

userSchema.methods.comparePassword = async function (enteredPassword, next) {
  try {
    const isMatch = await bcrypt.compare(enteredPassword, this.password);
    return isMatch;
  } catch (error) { return next(error) }
}

module.exports = mongoose.model("User", userSchema);
```

### Controller Usage (/api/controllers/auth.js)

Using the model created above, we can then use it in our controllers for signup and signin.

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

exports.signin = async function(req, res, next) {
  try {
    const user = await User.findOne({ email: req.body.email });
    const { id, username, password, profileImageUrl } = user;
    const isMatch = await user.comparePassword(req.body.password)

    if(isMatch) {
      let token = jwt.sign(
        {
          id,
          username,
          profileImageUrl
        },
        process.env.SECRET_KEY
      );
      return res.status(200).json({
        id,
        username,
        profileImageUrl,
        token
      });
    } else {
      return next({status: 400, message: 'Auth failed'})
    }
  } catch(e) {
    return next({status: 400, message: 'Auth failed'})
  }
}
```

### Router (/api/router/auth.js)

Then we just need to use them as routes.

```js
const express = require("express");
const router = express.Router();
const { signup, signin } = require("../controllers/auth");

router.post("/signup", signup)
router.post("/signin", signin)

module.exports = router;

```

### Associating with other Models

The 'Message' model that was referenced in the User model earlier:

```js
const mongoose = require("mongoose");
const User = require("./user");

const messageSchema = new mongoose.Schema(
  {
    text: {
      type: String,
      required: true,
      maxLength: 160
    },
    user: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "User"
    }
  },
  {
    timestamps: true
  }
);

messageSchema.pre('remove', async function(next){
  try {
    let user = await User.findById(this.user);
    user.messages.remove(this.id);
    await user.save();
    return next();
  } catch(e) {
    return next(e);
  }
})

const Message = mongoose.model("Message", messageSchema);
module.exports = Message
```

This stores a reference to the User that wrote the message and sets it up so that when removing the message, it also removes the referenced ID from the user itself.

We can then use this model in a handler/controller with:

```js
const Message = require("../models/message");
const User = require("../models/user");
const user = require("../models/user");

exports.createMessage = async function(req, res, next) {
  try {
    let message = await Message.create({
      text: req.body.text,
      user: req.params.id
    });
    let foundUser = await User.findById(req.params.id)

    foundUser.messages.push(message.id);
    await foundUser.save()
    let foundMessage = await Message.findById(message.id).populate("user", {
      username: true,
      profileImageUrl: true
    })
    return res.status(200).json(foundMessage);
  } catch(e) {
    return next(e)
  }
};

exports.getMessage = async function(req, res, next) {
  try {
    let message = await Message.findById(req.params.message_id);
    return res.status(200).json(message);
  } catch(e) { return next(e) }
};

exports.deleteMessage = async function(req, res, next) {
  try {
    let foundMessage = await Message.findById(req.params.message_id);
    await foundMessage.remove();
  } catch(e) { return next(e) }
};
```

In createMessage, we are using the ID from the route which will be appended with /api/users/:id/message. Then, after creating the message, we find the user and push the ID from the message into the reference of messages, which based on the user model, is the vector of message object IDs, then save the user. Then find the message that was just created while populating the user fields with the username and profile image to send back in the json response.

The deleteMessage cannot use anything like findOneAndDelete because it needs to trigger the pre remove functionality. It must be found first, then the remove method must be triggered, so that the message ID will be removed from the user.messages array.

The routes file will be:

```js
const express = require("express");
const router = express.Router({ mergeParams: true });
const { createMessage, getMessage, deleteMessage } = require("../handlers/messages");

router.route("/").post(createMessage);

router
  .route("/:message_id")
  .get(getMessage)
  .delete(deleteMessage)

module.exports = router;
```

The mergeParams property is needed as it allows the route to use req.params from the parent router which will contain the user ID with the parent path being '/api/users/:id/messages'.

Use middleware to authenticate and authorize users in required routes. In api/middleware/auth.js:

```js
const jwt = require('jsonwebtoken');

exports.loginRequired = function(req, res, next) {
  try {
    const token = req.headers.authorization.split(" ")[1];
    jwt.verify(token, process.env.SECRET_KEY, function(err, decoded) {
      if(decoded) {
        return next();
      } else {
        return next({
          status: 401,
          message: "Log in first"
        })
      }
    })
  } catch(e) {
    return next({
      status: 401,
      message: "Log in first"
    })
  }
}

exports.ensureCorrectUser = function(req, res, next) {
  try {
    const token = req.headers.authorization.split(" ")[1];
    jwt.verify(token, process.env.SECRET_KEY, function(err, decoded) {
      if (decoded && decoded.id == req.params.id) {
        return next();
      } else {
        return next({
          status: 401,
          message: "Unauthorized"
        })
      }
    })
  } catch(e) {
    return next({
      status: 401,
      message: "Unauthorized"
    })
  }
}
```
The req.headers.authorization will need to be split on a space and taking the second value from this because it will start with "Bearer <user-login-token>". Then we use the token to decode and match against the user id to make sure the user is authorized to access the route.

Finally, the app file will include:

```js
const messagesRoutes = require("./api/routes/messages");

app.use("/api/users/:id/messages", loginRequired, ensureCorrectUser, messagesRoutes);
```

Then, we can test the routes using httpie:

```bash
> http -f post localhost:5000/api/auth/signup username=hacker email=hacker@hacker.com password=hacker
> http -f post localhost:5000/api/auth/signin email=hacker@hacker.com password=hacker
> http -f post localhost:5000/api/users/5f04c3b01ab32188dbb13f56/messages "Authorization:Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImhhY2tlciIsImlhdCI6MTU5NDE0Nzc2MH0.RamUG4Q5Y-1l-hMI9qYYfa2f1_4_VkBQzZTiFMypkrk" text="hello world"
> http get localhost:5000/api/users/5f04a5d22b0acb6ac528e343/messages/5f04c0762d1b3f7aebaeff52 "Authorization:Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjVmMDRjM2IwMWFiMzIxODhkYmIxM2Y1NiIsInVzZXJuYW1lIjoiaGFja2VyIiwiaWF0IjoxNTk0MTUwODk4fQ.Yt2ikhJ2iAO_EJjx1gusoUY5wKY2P8EJh3Wtg7f3_DE"
> http delete localhost:5000/api/users/5f04c3b01ab32188dbb13f56/messages/5f04d38f0082d9d4462485a3 "Authorization:Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjVmMDRjM2IwMWFiMzIxODhkYmIxM2Y1NiIsInVzZXJuYW1lIjoiaGFja2VyIiwiaWF0IjoxNTk0MTUwODk4fQ.Yt2ikhJ2iAO_EJjx1gusoUY5wKY2P8EJh3Wtg7f3_DE"
```
