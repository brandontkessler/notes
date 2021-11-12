# Box dev setup

* Create a new box account
* Use same account and log in to box developer account
* Create a new app
	* Choose custom app
	* Choose OAuth 2.0 with JWT (Server Authentication)
	* give name then go to app
	* Change application access to Enterprise
	* Change Advanced Features to allow Perform Actions as Users
	* Hit Generate a Public/Private Keypair (this will download the file for you as well)
	* Hit Save Changes
	* Click on General in the side panel
	* Click Submit for Authorization
	* An email will come through requesting authorization, follow directions to authorize the app
* Go back to main box account
	* Go to admin console
	* Got to reports
	* Click user activity
	* In the filters, under Users click Created New User then go back to top and hit Run
	* Get the email address for the newly created user (the user will match AutomationUser_xyz@boxdevedition.com
	* Create a folder in the root directory called "dev"
	* Share that folder with the email address above, it will automatically accept the invite
* Python portion
	* `pip install boxsdk[jwt]`
	* Save the downloaded file saved when generating the public/private key pair step above as box_config.json
	* Get the ID of the folder by visiting it within the browser and copying the ID appended to the end of the url

```
import json
import os
from boxsdk import JWTAuth, Client

folder_id = '112116079195'

with open('box_config.json') as f:
		config_data = json.load(f)
		config = JWTAuth.from_settings_dictionary(config_data)
		client = Client(config)

dev_folder = client.folder(folder_id).get_items()
for item in dev_folder:
		print(item.name, item.id, item.object_type)
```
