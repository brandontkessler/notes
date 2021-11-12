
### Setup Geckodriver
In order to use Firefox browser, we need to download and install geckodriver, similarly, if we wanted to use Chrome instead, we need to download chromedriver.

[Gecko Driver](https://github.com/mozilla/geckodriver/releases/download/v0.24.0/geckodriver-v0.24.0-linux64.tar.gz)

After downloading, extract the geckodriver to the downloads folder, then open a terminal and run:

```
$ sudo mv ~/Downloads/geckodriver /usr/bin
```

### Install Selenium

```
$ pip install selenium
```


Test that it’s working in a py file

```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Firefox()
driver.get(“https://www.google.com”)
```

Running this will open google in the firefox browser.
