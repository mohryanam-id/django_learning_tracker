# Part 1

## Creating a project

```bash
django-admin startproject mysite
```

## Creating the Polls app

```bash
python manage.py startapp polls
```

## First View

- update polls/views.py content

  ```python
  from django.http import HttpResponse


  def index(request):
      return HttpResponse("Hello, world. You're at the polls index.")
  ```

## create polls/urls.py and update the content

```python
from django.urls import path

from . import views

urlpatterns = [
    path("", views.index, name="index"),
]
```

## update mysite/urls.py

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("polls/", include("polls.urls")),
    path("admin/", admin.site.urls),
]
```

## Run Server

```bash
python manage.py runserver
```
