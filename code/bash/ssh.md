# SSH Commands (OpenSSH - mac)

* **Process:** Store private key on local computer, add public key as an authorized key to remote servers, connect to remote server using the private key as identity.

* By default, ssh will read private key from `~/.ssh/id_rsa` file. If file has a different name, must provide an identity file (`ssh -i ~/.ssh/other_id_rsa user@hostname`). 

## Generating keys

## Other Concepts

* [Agent Forwarding](https://docs.github.com/en/developers/overview/using-ssh-agent-forwarding): Allows use of local SSH keys instead of leaving keys - without passphrases - sitting on your server.
* 


## Formatting config file

`nano ~/.ssh/config`


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


## Formatting authorized_keys file

`nano ~/.ssh/authorized_keys`




