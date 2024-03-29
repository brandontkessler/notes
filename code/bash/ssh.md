# SSH Commands (OpenSSH - mac)

* **Process:** Store private key on local computer, add public key as an authorized key to remote servers, connect to remote server using the private key as identity.

* By default, ssh will read private key from `~/.ssh/id_rsa` file. If file has a different name, must provide an identity file (`ssh -i ~/.ssh/other_id_rsa user@hostname`). 

## Generating keys

#### 1. Generate keys

```bash
> ssh-keygen -t rsa
```

More specifically:

```bash
> ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_servername
```

* The default file name and path is `~/.ssh/id_rsa` and corresponding public key `~/.ssh/id_rsa.pub`

#### 2. Copy public key to remote server

Use scp or rsync

```bash
> scp ~/.ssh/id_rsa.pub user@hostname:
```

Anything after the colon above is path but in this case we can leave it in root.

#### 3. Log in to remote server using username and password

#### 4. Create .ssh folder and authorized_keys file if none exists

```bash
> mkdir -p ~/.ssh && chmod 700 ~/.ssh && touch ~/.ssh/authorized_keys
```

#### 5. Add contents from public key to new line on authorized_keys file

```bash
> cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
```

#### 6. Delete the public key

```bash
> rm ~/id_rsa.pub
```

Now access can be made to the remote server using the private-public key pairing.


## Formatting config file

Build config file with: `touch ~/.ssh/config` and to make it readable/writeable only by the user add `chmod 600 ~/.ssh/config`.


The basic structure of the config file should like this: 

```bash
Host dev
    HostName dev.example.com
    User john
    Port 2322
```

Then running the command `ssh dev` will access the dev host with provided parameters. Other arguments for the config file can be viewed with `man ssh_config`.

Any number of hosts can be set up this way.

### Proxy Jumping Multiple Servers

Many remote servers have a bastion server as an entry-point meaning you have to first ssh into the bastion server then ssh to the remote server.

#### Method 1

* Ensure ForwardAgent is enabled for all hosts - done using `Host *` (Other regex matching works to specify hosts)
* Set up the config file to connect to the bastion host using an identity file if necessary (if not at `~/.ssh/id_rsa`)
* Set up the remote server to jump from bastion to the remote server using the same identity provided

```bash
# ~/.ssh/config
Host *
  ForwardAgent yes
  
Host bastion
  Hostname public.domain.com
  User alex
  Port 50482
  IdentityFile ~/.ssh/id_ed25519
  
Host lanserver
  Hostname 192.168.1.1
  User alex
  ProxyJump bastion
```

Then, connecting to the remote server uses `ssh lanserver`

#### Method 2

Sometimes there are unknown details to the remote server if set up by someone else. For example, someone in IT sets up the bastion and remote server and provides the hostname to the bastion and then just a command to then connect to the remote server from the bastion.

The example below provides a bastion of mux1.accuenbi.com with username and identity file provided. [Request TTY](https://unix.stackexchange.com/questions/27713/ssh-config-way-to-specify-pseudo-tty-allocation-and-command-execution) allows for running a command from the bastion host once inside with that command being `ssh mta1`. The result is running ssh into the bastion host, then running `ssh mta` from the bastion host and moving into the remote server.

```bash
# ~/.ssh/config
Host ec2    
        HostName mux1.accuenbi.com
        User brandon_kessler
        Port 22
        IdentityFile ~/.ssh/mux1_id_rsa
        RequestTTY force
        RemoteCommand ssh mta1
```

## [Password Management](https://www.freecodecamp.org/news/how-to-manage-multiple-ssh-keys/)

To avoid having to provide a password every time SSH connection is initiating, we can add it to MacOS keychain.

```bash
ssh-add -K ~/.ssh/id_rsa_whatever
```

You can remove keys from ssh-agent with (this also happens when your computer restarts):

```
ssh-add -D
```

This will then require you to enter a password again even though the `ssh-add -K` is still in the keychain.

Bypass this by updating the config file:

```bash
# ~/.ssh/config
Host *
  AddKeysToAgent yes
  UseKeychain yes
```

Now, SSH will look for key in keychain and if it finds it you will not be prompted for password. Key will also be added to ssh-agent.

## Other Concepts

* [Agent Forwarding](https://docs.github.com/en/developers/overview/using-ssh-agent-forwarding): Allows use of local SSH keys instead of leaving keys - without passphrases - sitting on your server.
