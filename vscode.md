# Setup

## Usage/Hotkeys

* Open terminal: ctrl + `
* Open a second terminal: ctrl + shift + `

## General Setup

### General Extensions

* Code Runner
* vscode-icons
* Dracula Official
* markdownlint
* Markdown All in One

### Settings

* `cmd + ,` opens settings
* Edit User settings to edit global settings
* Edit Workspace settings to edit just the settings for this project

## Markdown

* Create a .markdown.json in root directory
* Add:

```json
{
    "default": true,
    "MD007": false,
    "no-hard-tabs": false,
    "line-length": false
}
```

## R setup

### R Extensions

* R

## Connect to Linux Server

* Open terminal
* Generate a new private/public key pair
	* `$ ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_servername`
	* ex. `$ ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_optimus`
	* this generates a keypair saved in ~/.ssh
* Upload public key to server that connects to the private key
	* `$ ssh-copy-id -f -i ~/.ssh/id_rsa_servername.pub username@ip_address`
	* ex. `$ ssh-copy-id -f -i ~/.ssh/id_rsa_optimus.pub brandonkessler@100.96.185.40`
* Access server with:
	* ``ssh -i ~/.ssh/id_rsa_servername username@IP``
	* This won’t require a password to login
* Go to extensions and search “remote development”
* Install the Remote Development extension which will install a few extensions including Remote - SSH
* Open command pallet (cmd + p)
	* type `> remote-ssh con` and select “Remote-SSH Connect to Host…”
	* Select “+ Add New SSH Host…”
	* Add the command to ssh using rsa keys:
		* `ssh -i ~/.ssh/id_rsa_servername username@IP`
	* hit enter
	* Select the ssh configuration file to update:
		* ex. “/Users/username/.ssh/config”
* Now any time you select connect to host or click the greater than less than symbol in the bottom right corner and select connect to host, then select the IP address for the server to connect to and it will connect directly.

## Connect to Database

* Download postgresql extension
* Reload vs code if necessary
* Select the Postgres icon from the navigation pane
* hit the + symbol to add a new database

* Download and install
* During setup, select postgresql
* Host: `url_to_host`
* Database: `db_name`
* Port: 5439
* User: my_username
* PW: fun_password
