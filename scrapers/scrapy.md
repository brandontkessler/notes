
### Install
```
$ pip install scrapy
$ pip install scrapy-user-agents

$ scrapy startproject <project name>
$ cd <project name>

$ scrapy genspider <spider name> <starting url>
```

### Setup downloader middlewares

In the settings.py file, add:

```
DOWNLOADER_MIDDLEWARES = {
 ‘scrapy.downloadermiddlewares.useragent.UserAgentMiddleware’: None,
 ‘scrapy_user_agents.middlewares.RandomUserAgentMiddleware’: 400,
}
```
