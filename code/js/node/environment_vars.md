# Env Vars

For dev, nodemon has it built in. In the root, create a nodemon.json file and include:

```json
{
  "env": {
    "NODE_ENV": "dev",
    "DB_PASSWORD": "my_fun_password"
  }
}
```

These can now be accessible anywhere with `process.env.DB_PASSWORD`.
