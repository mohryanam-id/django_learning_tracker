# Section 3 - Writing your first Django app

## Part 1

- Creating a project

```bash
django-admin startproject mysite
```

- Creating the Polls app

```bash
python manage.py startapp polls
```

- First View

  - update polls/views.py content

    ```python
    from django.http import HttpResponse


    def index(request):
        return HttpResponse("Hello, world. You're at the polls index.")
    ```

  - create polls/urls.py and update the content

    ```python
    from django.urls import path

    from . import views

    urlpatterns = [
        path("", views.index, name="index"),
    ]
    ```

  - update mysite/urls.py

    ```python
    from django.contrib import admin
    from django.urls import include, path

    urlpatterns = [
        path("polls/", include("polls.urls")),
        path("admin/", admin.site.urls),
    ]
    ```

- Run Server

```bash
python manage.py runserver
```

## Part 2

- Database Setup

```bash
python manage.py migrate
```

- Creating Models on polls/models.py

```python
import datetime
from django.db import models
from django.utils import timezone


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")

    def __str__(self):
        return self.question_text

    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    def __str__(self):
        return self.choice_text
```

- Activating Model on mysite/settings.py

```python
INSTALLED_APPS = [
    "polls.apps.PollsConfig",
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
]
```

to create migrations for models changes

```bash
python manage.py makemigrations polls
```

to check SQL

```bash
python manage.py sqlmigrate polls 0001
```

to apply changes

```bash
python manage.py migrate
```

- Play with the API

```bash
python manage.py shell
```

```python
>>> from polls.models import Choice, Question
>>> from django.utils import timezone
>>> Question.objects.all()
>>> q = Question(question_text="What's new?", pub_date=timezone.now())
>>> q.save()
>>> q.id
>>> q.question_text
>>> q.pub_date
>>> q.question_text = "What's up?"
>>> q.save()
>>> Question.objects.all()
>>> q.choice_set.create(choice_text="Not much", votes=0)
>>> q.choice_set.create(choice_text="The sky", votes=0)
>>> c = q.choice_set.create(choice_text="Just hacking again", votes=0)
>>> current_year = timezone.now().year
>>> Choice.objects.filter(question__pub_date__year=current_year)
>>> c = q.choice_set.filter(choice_text__startswith="Just hacking")
>>> c.delete()
```

- Introducing the Django Admin

  - Creating an admin user

  ```bash
  python manage.py createsuperuser
  Username: admin
  Email address: admin@example.com
  Password: **********
  Password (again): *********
  ```

  - Update polls/admin.py

  ```python
  from django.contrib import admin
  from .models import Question

  admin.site.register(Question)
  ```

  - Start the Development Server

  ```bash
  python manage.py runserver
  ```

  - Open [/admin/](http://127.0.0.1:8000/admin/) on your local domain
