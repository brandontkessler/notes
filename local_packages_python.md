
## Setup PYTHONPATH
Use the PYTHONPATH environment variable and set it equal to the path to your packages. This will allow you to create and save all of your packages in one location, and import them directly into your projects.

#### Windows
* Search for “env”
* Click ‘Edit the system environment variables’
* Click ‘Environment Variables’
[image:B371BC1C-B057-497F-9B7B-29071D19F828-852-0004A291BF4AA128/1*q9Pye1QBufDLMQxkiYvt8w.png]
* Under ‘User variables’ click ‘New’
* Variable name is equal to ‘PYTHONPATH’ and variable value is equal to path of packages, for example:
	* ‘C:\Users\username\Desktop\example\pkgs’

#### Linus/Mac
Update .bashrc file or . bash_profile to add the path
* `$ nano ~/.bashrc` or `$ nano ~/.bash_profile`
* Then anywhere write: `export PYTHONPATH=‘/mnt/c/Users/username/Desktop/example/pkgs’`
* path for Mac should look like `‘/Users/brandon.kessler/Desktop/example/pkgs’`
* Restart bashrc with: `. ~/.bashrc`
