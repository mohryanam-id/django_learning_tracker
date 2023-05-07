# PART 4: Forms and Generic Views

## Write Minimal Form

### Detail Form

- polls/templates/polls/detail.html

```html
<form action="{% url 'polls:vote' question.id %}" method="post">
  {% csrf_token %}
  <fieldset>
    <legend><h1>{{ question.question_text }}</h1></legend>
    {% if error_message %}
    <p><strong>{{ error_message }}</strong></p>
    {% endif %} {% for choice in question.choice_set.all %}
    <input
      type="radio"
      name="choice"
      id="choice{{ forloop.counter }}"
      value="{{ choice.id }}"
    />
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label
    ><br />
    {% endfor %}
  </fieldset>
  <input type="submit" value="Vote" />
</form>
```

- polls/urls.py

```python
path("<int:question_id>/vote/", views.vote, name="vote"),
```

### Vote View

- polls/views.py

```python
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST["choice"])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(
            request,
            "polls/detail.html",
            {
                "question": question,
                "error_message": "You didn't select a choice.",
            },
        )
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse("polls:results", args=(question.id,)))
```

### Result Form

- polls/view.py

```python
from django.shortcuts import get_object_or_404, render


def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, "polls/results.html", {"question": question})
```

- polls/results.html

```html
<h1>{{ question.question_text }}</h1>

<ul>
  {% for choice in question.choice_set.all %}
  <li>
    {{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize
    }}
  </li>
  {% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```

## Generic Views: Less Code is Better

### Amend URLconf on polls/urls.py

```python
from django.urls import path

from . import views

app_name = "polls"
urlpatterns = [
    path("", views.IndexView.as_view(), name="index"),
    path("<int:pk>/", views.DetailView.as_view(), name="detail"),
    path("<int:pk>/results/", views.ResultsView.as_view(), name="results"),
    path("<int:question_id>/vote/", views.vote, name="vote"),
]
```

### Amend Views

```python
from django.http import HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic

from .models import Choice, Question


class IndexView(generic.ListView):
    template_name = "polls/index.html"
    context_object_name = "latest_question_list"

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by("-pub_date")[:5]


class DetailView(generic.DetailView):
    model = Question
    template_name = "polls/detail.html"


class ResultsView(generic.DetailView):
    model = Question
    template_name = "polls/results.html"


def vote(request, question_id):
    ...  # same as above, no changes needed.

```
