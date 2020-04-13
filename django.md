# Django

Documentation on django projects.

Building a blog app as an example.

### Setup

```
$ python3 -m venv venv
$ source venv/bin/activate
$ pip install django
$ django-admin

$ django-admin startproject django_proj
$ cd django_proj
$ python3 manage.py runserver
```

### Building an App

##### Setup

Django projects use many "apps" within a single project. Steps to creating an app:

* Start an app via terminal with `$python manage.py startapp name_of_app`

```
$ python3 manage.py startapp blog
```

* Add its config to the settings.py file of main project directory by copying the class name in apps.py file. Given the app was named "blog", the class name should be BlogConfig. Path would be 'blog.apps.BlogConfig'.

##### Create Views

Open views.py from the blog app. Need http response for a route:

```
from django.shortcuts import render
from django.http import HttpResponse

def home(request):
  return HttpResponse('<h1>Blog Home</h1>')
```

Create urls.py file in the blog app to map the route containing:

```
from django.urls import path
from . import views

urlpatterns = [
  path('', views.home, name='blog-home')
]
```

This has now been specified as the home route by including nothing in path. It is mapped to views.home which is the name of the route created in views.py. It has been given a name of 'blog-home'.

Add to urls within main project. Edit the urls.py file within the main directory (django_proj) and include the function from django urls.

```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
  path('admin/', admin.site.urls),
  path('blog/', include('blog.urls')),
]
```

Now visiting localhost:8000/blog directs to the view just created.

New routes are added by creating another view and adding to the urls.py file in the app. This is already mapped to the main app's urls.py file.

For example, in views.py:

```
def about(request):
  return HttpResponse('<h1>Blog About</h1>')
```

Then in urls.py:

```
…
urlpatterns = [
  …
  path('about/', views.about, name='blog-about'),
]
```

Visit localhost:8000/blog/about/

##### Templates
