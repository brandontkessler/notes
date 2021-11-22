# Setup

## Usage/Hotkeys

* Open terminal: ctrl + `
* Open a second terminal: ctrl + shift + `

## General Setup

### General Extensions

* Code Runner
* vscode-icons
* Tokyo Night
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

* Follow [ssh configuration setup](https://github.com/brandontkessler/notes/blob/master/code/bash/ssh.md)
* Add [Remote - SSH extension](https://medium.com/@christyjacob4/using-vscode-remotely-on-an-ec2-instance-7822c4032cff)
* Then connect to server using one of the Hosts created in the `~/.ssh/config` file.

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
