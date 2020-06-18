# Env Vars

For dev, nodemon has it built in. In the root, create a nodemon.json file and include:

```json
{
  "env": {
    "DB_PASSWORD": "my_fun_password"
  }
}
```

These can now be accessible anywhere with `process.env.DB_PASSWORD`.
