# PART 3: Views and Templates

## Writing More Views on polls/views.py, Wiring them to polls/urls.py and Using template system

### polls/views.py

```python
def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    context = {"latest_question_list": latest_question_list}
    return render(request, "polls/index.html", context)


def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, "polls/detail.html", {"question": question})


def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)


def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

### polls/urls.py

```python
urlpatterns = [
    # ex: /polls/
    path("", views.index, name="index"),
    # ex: /polls/5/
    path("<int:question_id>/", views.detail, name="detail"),
    # ex: /polls/5/results/
    path("<int:question_id>/results/", views.results, name="results"),
    # ex: /polls/5/vote/
    path("<int:question_id>/vote/", views.vote, name="vote"),
]
```

### polls/templates/polls/index.html

```html
{% if latest_question_list %}
<ul>
  {% for question in latest_question_list %}
  <li>
    <a href="{% url 'detail' question.id %}">{{ question.question_text }}</a>
  </li>
  {% endfor %}
</ul>
{% else %}
<p>No polls are available.</p>
{% endif %}
```

### polls/templates/polls/detail.html

```html
{{ question }}
<h1>{{ question.question_text }}</h1>
<ul>
  {% for choice in question.choice_set.all %}
  <li>{{ choice.choice_text }}</li>
  {% endfor %}
</ul>
```
