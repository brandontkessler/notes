Scheduling python scripts with cronjobs utilizing virtual environments.

* Store the virtual environment somewhere in the root, like in “~/.pyenv/myvenv”.
* In terminal, run `$ crontab -e`
* Add the following job:

```
0 */1 * * * "$(command -v bash)" -c 'cd /home/server_name && source .pyenv/myvenv/bin/activate && python3 execute.py && deactivate'
```

That will run  the job every hour.