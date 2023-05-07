# PART 5: Testing

## Writing tests

```python
...
class QuestionModelTests(TestCase):
    def test_was_published_recently_with_future_question(self):
        ...
    def test_was_published_recently_with_old_question(self):
        ...

    def test_was_published_recently_with_recent_question(self):
        ...


def create_question(question_text, days):
    ...


class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        ...

    def test_past_question(self):
        ...

    def test_future_question(self):
        ...

    def test_future_question_and_past_question(self):
        ...

    def test_two_past_questions(self):
        ...


class QuestionDetailViewTests(TestCase):
    def test_future_question(self):
        ...

    def test_past_question(self):
        ...

```

## Running tests

```python
python manage.py test polls
```
