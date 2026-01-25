# Django Complete Course

![Bidur Sapkota](https://www.bidursapkota.com.np/images/gravatar.webp "Bidur Sapkota - Developer")&nbsp;[Bidur Sapkota](https://www.bidursapkota.com.np/)

![Django Complete Guide by Bidur Sapkota](/images/unit-3/13-django-post-1200.webp "Django Complete Guide – Blog by Bidur Sapkota")

## Table of Content

- [Getting Started](#getting-started)
- [Django Setup](#django-setup)
- [URLs & Views](#urls--views)
- [Templates & Static Files](#templates--static-files)
- [Data and Models](#data-and-models)

---

## Getting Started

### What is Django?

- Django is a high-level Python web framework
- Follows the MTV (Model-Template-View) architecture pattern
- Built-in features: admin panel, ORM, authentication, security
- "Batteries included" philosophy - comes with everything you need

**Why Django?**

1. Rapid development
2. Secure by default
3. Scalable architecture
4. Large community and excellent documentation

---

### The Course Prerequisites

- Basic Python knowledge (variables, functions, classes, loops)
- Understanding of HTML/CSS basics
- Familiarity with command line/terminal
- A computer with internet access
- Basic understanding of databases
- Experience with any text editor/IDE

---

---

---

## Django Setup

### Installing Python & Django

**Install Python**

```bash
# Download Python from python.org
# Verify installation
python --version
# or
python3 --version
```

**Create Virtual Environment**

```bash
# Create project folder
mkdir django_course
cd django_course

# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate
```

**Install Django**

```bash
pip install django
# Verify installation
django-admin --version
```

---

### Creating a Django Project

```bash
# Create new Django project
django-admin startproject myproject

# Project structure created:
# myproject/
#   ├── manage.py
#   └── myproject/
#       ├── __init__.py
#       ├── settings.py
#       ├── urls.py
#       ├── asgi.py
#       └── wsgi.py
```

---

### Installing an IDE

**Recommended: Visual Studio Code**

1. Download from code.visualstudio.com
2. Install Python extension
3. Install Pylance extension
4. Install autopep8 extension
5. Install Django extension
6. Configure Python interpreter to use virtual environment

```json
// .vscode/settings.json
{
  "python.pythonPath": "/usr/local/bin/python3",
  "window.zoomLevel": 6,
  "python.languageServer": "Pylance"
}
```

---

### Analyzing the Created Project

| File          | Purpose                                            |
| ------------- | -------------------------------------------------- |
| `manage.py`   | Command-line utility for project management        |
| `settings.py` | Project configuration (database, apps, middleware) |
| `urls.py`     | URL routing configuration                          |
| `wsgi.py`     | Web Server Gateway Interface for deployment        |
| `asgi.py`     | Asynchronous Server Gateway Interface              |

---

### Starting a Development Server

```bash
# Navigate to project folder
cd myproject

# Start development server
python manage.py runserver

# Server runs at http://127.0.0.1:8000/
# Press Ctrl+C to stop
```

**Custom Port:**

```bash
python manage.py runserver 8080
```

---

### Django Apps

Apps are modular components of a Django project.

**Creating an App:**

```bash
python manage.py startapp myapp
```

**App Structure:**

```text
myapp/
├── __init__.py
├── admin.py      # Admin panel configuration
├── apps.py       # App configuration
├── models.py     # Database models
├── tests.py      # Unit tests
├── views.py      # View functions/classes
└── migrations/   # Database migrations
```

**Registering App in settings.py:**

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    # ... other apps
    'myapp',  # Add your app here
]
```

---

## URLs & Views

### Creating a New Project

```bash
# Create fresh project for this module
django-admin startproject urlsviews_project
cd urlsviews_project
python manage.py startapp challenges
```

---

### What are URLs & Views?

**URLs:** Map web addresses to Python functions
**Views:** Python functions that handle requests and return responses

**Flow:**

```text
User Request → URL Pattern → View Function → Response
```

---

### Creating a First View & URL

**Create View (challenges/views.py)**

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Welcome to the Challenges App!")
```

**Create App URLs (challenges/urls.py)**

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

**Include in Project URLs (urlsviews_project/urls.py)**

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('challenges/', include('challenges.urls')),
]
```

---

### Adding More Views & URLs

```python
# challenges/views.py
def january(request):
    return HttpResponse("January Challenge: Exercise daily!")

def february(request):
    return HttpResponse("February Challenge: Read a book!")

# challenges/urls.py
urlpatterns = [
    path('', views.index, name='index'),
    path('january/', views.january, name='january'),
    path('february/', views.february, name='february'),
]
```

---

### Dynamic Path Segments & Captured Values

```python
# challenges/urls.py
urlpatterns = [
    path('<month>/', views.monthly_challenge, name='monthly-challenge'),
]

# challenges/views.py
def monthly_challenge(request, month):
    return HttpResponse(f"Challenge for {month}")
```

---

### Path Converters

**Available Converters:**

- `str` - Matches any non-empty string (default)
- `int` - Matches positive integers
- `slug` - Matches slug strings (letters, numbers, hyphens, underscores)
- `uuid` - Matches UUID strings
- `path` - Matches any string including slashes

```python
# Examples
path('<str:month>/', views.monthly_challenge),
path('<int:month>/', views.monthly_challenge_by_number),
path('<slug:title>/', views.post_detail),
```

---

### Adding More Dynamic View Logic

```python
from django.http import HttpResponse, HttpResponseNotFound

# challenges/views.py
monthly_challenges = {
    'january': 'Exercise daily for 30 minutes',
    'february': 'Read one book',
    'march': 'Learn something new each day',
    'april': 'Drink at least 2 liters of water daily',
    'may': 'Wake up early every day',
    'june': 'Practice a new skill for 20 minutes daily',
    'july': 'Avoid junk food for the entire month',
    'august': 'Write a daily journal entry',
    'september': 'Learn and revise one topic each day',
    'october': 'Limit social media usage to 30 minutes per day',
    'november': 'Express gratitude by writing one thankful note daily',
    'december': 'Reflect on the year and plan goals for next year',
}

def monthly_challenge(request, month):
    try:
        challenge_text = monthly_challenges[month.lower()]
        return HttpResponse(challenge_text)
    except KeyError:
        return HttpResponseNotFound("This month is not supported!")
```

---

### Redirects

```python
from django.http import HttpResponseRedirect
from django.shortcuts import redirect

# Method 1: HttpResponseRedirect
def old_url(request):
    return HttpResponseRedirect('/challenges/january/')

# Method 2: redirect shortcut (preferred)
def monthly_challenge_by_number(request, month):
    months = list(monthly_challenges.keys())
    if month > len(months):
        return HttpResponse("Invalid month", status=404)
    return redirect(f'/challenges/{months[month-1]}/')
```

---

### The Reverse Function & Named URLs

```python
from django.urls import reverse

def monthly_challenge_by_number(request, month):
    months = list(monthly_challenges.keys())
    redirect_month = months[month - 1]
    # Using reverse with named URL
    redirect_url = reverse('monthly-challenge', args=[redirect_month])
    return redirect(redirect_url)
```

---

### Returning HTML

```python
def index(request):
    html_content = """
    <html>
        <head><title>Challenges</title></head>
        <body>
            <h1>Monthly Challenges</h1>
            <ul>
                <li><a href="/challenges/january/">January</a></li>
                <li><a href="/challenges/february/">February</a></li>
            </ul>
        </body>
    </html>
    """
    return HttpResponse(html_content)
```

```py
def monthly_challenge(request, month):
    try:
        challenge_text = monthly_challenges[month.lower()]
        response_data = f"<h1>{challenge_text}</h1>"
        return HttpResponse(response_data)
    except KeyError:
        return HttpResponseNotFound("<h1>This month is not supported!</h1>")
```

**Dynamic generation of html with list of links**

```py
def index(request):
    list_items = ""
    months = list(monthly_challenges.keys())

    for month in months:
        capitalized_month = month.capitalize()
        month_path = reverse("month-challenge", args=[month])
        list_items += f"<li><a href=\"{month_path}\">{capitalized_month}</a></li>"

    # "<li><a href="...">January</a></li><li><a href="...">February</a></li>..."

    response_data = f"<ul>{list_items}</ul>"
    return HttpResponse(response_data)
```

---

---

---

## Templates & Static Files

### Adding & Registering Templates

**Create templates folder**

```text
challenges/
└── templates/
    └── challenges/
        └── challenge.html
```

**Register app in settings.py**

```python
INSTALLED_APPS = [
    # ...
    'challenges',
]
```

---

### Challenge Template

**Add content in `challenges/templates/challenges/challenge.html`**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <h1>This Month's Challenge</h1>
    <h2>Challenge text</h2>
  </body>
</html>
```

### Rendering Templates

**Using render_to_string**

```py
# challenges/views.py
from django.http import HttpResponse, HttpResponseNotFound, HttpResponseRedirect
from django.urls import reverse
from django.template.loader import render_to_string

def monthly_challenge(request, month):
    try:
        challenge_text = monthly_challenges[month.lower()]
        response_data = render_to_string("challenges/challenge.html")
        return HttpResponse(response_data)
    except:
        return HttpResponseNotFound("<h1>This month is not supported!</h1>")
```

**Using render**

```python
# challenges/views.py
from django.shortcuts import render
from django.http import HttpResponse, HttpResponseNotFound, HttpResponseRedirect
from django.urls import reverse

def monthly_challenge(request, month):
    try:
        challenge_text = monthly_challenges[month.lower()]
        return render(request, "challenges/challenge.html")
    except:
        return HttpResponseNotFound("<h1>This month is not supported!</h1>")
```

---

### Template Language & Variable Interpolation

```html
<!-- challenges/templates/challenges/challenge.html -->
<h1>This Month's Challenge</h1>
<h2>{{ text }}</h2>
```

```python
# challenges/views.py
def monthly_challenge(request, month):
    try:
        challenge_text = monthly_challenges[month.lower()]
        return render(request, "challenges/challenge.html", {
            "text": challenge_text
        })
    except:
        return HttpResponseNotFound("<h1>This month is not supported!</h1>")
```

**Adding month name too**

```html
<head>
  <title>{{ month_name }} Challenge</title>
</head>
<body>
  <h1>{{ month_name }} Challenge</h1>
  <h2>{{ text }}</h2>
</body>
```

```py
def monthly_challenge(request, month):
    try:
        challenge_text = monthly_challenges[month.lower()]
        return render(request, "challenges/challenge.html", {
            "text": challenge_text,
            "month_name": month.capitalize()
        })
    except:
        return HttpResponseNotFound("<h1>This month is not supported!</h1>")
```

---

### Filters

**Common Filters:**

```html
{{ name|title }}
<!-- Capitalizes each word -->
{{ text|truncatewords:30 }}
<!-- Limits to 30 words -->
{{ date|date:"D d M Y" }}
<!-- Date formatting -->
{{ text|default:"N/A" }}
<!-- Default value -->
{{ text|length }}
<!-- String/list length -->
{{ html|safe }}
<!-- Mark as safe HTML -->
```

```html
<head>
  <title>{{ month_name|title }} Challenge</title>
</head>
<body>
  <h1>{{ month_name|title }} Challenge</h1>
  <h2>{{ text }}</h2>
</body>
```

---

### Tags & the "for" Tag

**First, update index view with template**

**Create `challenges/templates/challenges/index.html` template**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>All Challenges</title>
  </head>
  <body>
    <ul>
      <li><a href="/challenges/january">January</a></li>
      <li><a href="/challenges/february">February</a></li>
      <li><a href="/challenges/march">March</a></li>
      <li><a href="/challenges/april">April</a></li>
      <li><a href="/challenges/may">May</a></li>
      <li><a href="/challenges/june">June</a></li>
      <li><a href="/challenges/july">July</a></li>
      <li><a href="/challenges/august">August</a></li>
      <li><a href="/challenges/september">September</a></li>
      <li><a href="/challenges/october">October</a></li>
      <li><a href="/challenges/november">November</a></li>
      <li><a href="/challenges/december">December</a></li>
    </ul>
  </body>
</html>
```

```py
# challenges/views.py
def index(request):
    months = list(monthly_challenges.keys())

    return render(request, "challenges/index.html", {
        "months": months
    })
```

```html
<!-- challenges/templates/challenges/index.html -->
<ul>
  {% for month in months %}
  <li>
    <a href="/challenges/{{month}}">
      {{ forloop.counter }} - {{ month|title }}
    </a>
  </li>
  {% endfor %}
</ul>
```

---

### The URL Tag for Dynamic URLs

```html
<!-- challenges/templates/challenges/index.html -->
<ul>
  {% for month in months %}
  <li>
    <a href="{% url 'monthly-challenge' month %}">
      {{ forloop.counter }} - {{ month|title }}
    </a>
  </li>
  {% endfor %}
</ul>
```

---

### The "if" Tag for Conditional Content

**Update monthly challenge dictionary**

```py
# challenges/views.py
monthly_challenges = {
    'january': 'Exercise daily for 30 minutes',
    # ...
    'december': None
}
```

```html
<!-- challenges/templates/challenges/challenge.html -->
<h1>{{ month_name|title }} Challenge</h1>
{% if text is not None %}
<h2>{{ text }}</h2>
{% else %}
<p>There is no challenge for this month yet!</p>
{% endif %}
```

---

### Template Inheritance

**Update settings.py to add global templates folder location**

```py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            BASE_DIR / "templates"
        ],
        'APP_DIRS': True,
        # ...
    }
]
```

**Create `templates/base.html` (Parent Template):**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{% block page_title %}My Challenges{% endblock %}</title>
  </head>
  <body>
    {% block content %}{% endblock %}
  </body>
</html>
```

**Child Template:**

```html
<!-- challenges\templates\challenges\index.html -->
<!-- pre tag is not required, just for proper blog format -->
<pre>
{% extends "base.html" %}

{% block page_title %}
  All Challenges
{% endblock %}

{% block content %}
  <ul>
    {% for month in months %}
      <li><a href="{% url 'monthly-challenge' month %}">{{ month|title }}</a></li>
    {% endfor %}
  </ul>
{% endblock %}
</pre>
```

```html
<!-- challenges\templates\challenges\challenge.html -->
<pre>
{% extends "base.html" %}

{% block page_title %}
  {{ month_name|title }} Challenge
{% endblock %}
  
{% block content %}
  <h1>{{ month_name|title }} Challenge</h1>
  {% if text is not None %}
    <h2>{{ text }}</h2>
  {% else %}
    <p>There is no challenge for this month yet!</p>
  {% endif %}
{% endblock %}
</pre>
```

---

### Including Partial Template Snippets

**Create `challenges\templates\challenges\includes\header.html`**

```html
<header>
  <nav>
    <a href="{% url "index" %}">All Challenges</a>
  </nav>
</header>
```

**Add include header to both index and challenge template**

```html
<pre>
{% block content %}
  {% include "challenges/includes/header.html" %}
  <!-- ... -->
{% endblock %}
</pre>
```

---

### 404 Templates

**Create `templates/404.html`:**

```html
<pre>
{% extends "base.html" %}

{% block page_title %}
  Something went wrong - we could not find that page!
{%endblock%}

{% block content %}
  <h1>We could not find that page!</h1>
  <p>Sorry, but we could not find a matching page!</p>
{% endblock %}
</pre>
```

```py
from django.http import Http404

def monthly_challenge(request, month):
    try:
        challenge_text = monthly_challenges[month.lower()]
        return render(request, "challenges/challenge.html", {
            "text": challenge_text,
            "month_name": month
        })
    except:
        raise Http404()
```

**Note:** Set `DEBUG = False` and `ALLOWED_HOSTS = ['*']` in settings.py to see custom 404 pages.

---

### Adding Static Files

**Create static folder**

```text
challenges/
└── static/
    └── challenges/
        ├── css/
        │   └── challenges.css
        └── images/
            └── logo.png
```

**Load in template**

```css
/* challenges\static\challenges\css\challenges.css */
ul {
  list-style: none;
}
```

```html
<!-- templates\base.html -->
<head>
  {% block css_files %}{% endblock %}
</head>
```

```html
<!-- challenges\templates\challenges\index.html -->
<pre>
{% load static %}

{% block css_files %}
  <link rel="stylesheet" href="{% static 'challenges/css/challenges.css' %}">
{% endblock %}
</pre>
```

---

### Adding Global Static Files

**settings.py:**

```python
STATIC_URL = '/static/'

STATICFILES_DIRS = [
    BASE_DIR / 'static',  # Global static files
]
```

**Create static\styles.css**

```css
@import url("https://fonts.googleapis.com/css2?family=Roboto+Condensed:wght@400;700&display=swap");

html {
  font-family: "Roboto Condensed", sans-serif;
}

body {
  margin: 0;
  background-color: #1a1a1a;
}
```

```html
<!-- templates\base.html -->
<pre>
{% load static %}

<head>
    <link rel="stylesheet" href="{% static "styles.css" %}">
    {% block css_files %}{% endblock %}
</head>
</pre>
```

### More CSS

**Create `challenges\static\challenges\includes\header.css`**

```css
header {
  width: 100%;
  height: 5rem;
  background-color: #353535;
}

header nav {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

header nav a {
  color: white;
  font-size: 2rem;
  font-weight: bold;
  text-decoration: none;
}

header nav a:hover,
header nav a:active {
  color: #cf54a6;
}
```

**Create `challenges\static\challenges\css\challenge.css`**

```css
h1,
h2 {
  text-align: center;
  color: white;
}

h1 {
  font-size: 1.5rem;
  margin: 2rem 0 1rem 0;
  font-weight: normal;
  color: #cf54a6;
}

h2 {
  font-size: 3rem;
  font-weight: bold;
}

.fallback {
  text-align: center;
  color: white;
}
```

**Update `challenges\static\challenges\css\challenges.css`**

```css
ul {
  list-style: none;
  margin: 2rem auto;
  width: 90%;
  max-width: 20rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  height: 30rem;
  background-color: #eeeeee;
}

li {
  margin: 1rem 0;
  text-align: center;
  font-size: 1.5rem;
  border-bottom: 1px solid #ccc;
  padding-bottom: 1rem;
}

li:last-of-type,
li:nth-of-type(6) {
  border-bottom: none;
}

li a {
  text-decoration: none;
  color: #383838;
}

li a:hover,
li a:active {
  color: #7e0154;
}
```

**Update `challenges\templates\challenges\challenge.html`**

```html
<pre>
{% extends "base.html" %}
{% load static %}

{% block css_files %}
  <link rel="stylesheet" href="{% static "challenges/challenge.css" %}">
  <link rel="stylesheet" href="{% static "challenges/includes/header.css" %}">
{% endblock %}
</pre>
```

**Update `challenges\templates\challenges\index.html`**

```html
<pre>
{% extends "base.html" %}
{% load static %}

{% block css_files %}
  <link rel="stylesheet" href="{% static "challenges/challenges.css" %}">
  <link rel="stylesheet" href="{% static "challenges/includes/header.css" %}">
{% endblock %}
</pre>
```

---

---

---

---

---

---

## Data and Models

### What is ORM?

ORM (Object-Relational Mapping) is a technique that lets you work with databases using your programming language's objects instead of writing SQL queries.

**Benefits of ORM:**

- Write Python (or other language) instead of SQL
- Database-agnostic code
- Automatic SQL injection protection
- Easier to maintain and read
- Object-oriented database access

**How ORM Works:**

![Class to Table Mapping](/images/unit-3/class-to-table-mapping.webp)

**Supported Databases:**

- SQLite (default, file-based)
- PostgreSQL (recommended for production)
- MySQL
- Oracle

**Create fresh project**

```bash
# Create fresh project for this module
django-admin startproject book_store
cd book_store
python manage.py startapp book_outlet
```

**Register in settings.py:**

```python
INSTALLED_APPS = [
    # ...
    'book_outlet',
]
```

### Django ORM

Django includes a powerful built-in ORM. You define models as Python classes, and Django handles the database operations.

**Defining Models**

Models are defined in `models.py`:

```py
from django.db import models

class Book(models.Model):
  title = models.CharField(max_length=50)
  rating = models.IntegerField()
```

**Equivalent to sql below after we migrate**

```sql
CREATE TABLE books (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  rating INTEGER NOT NULL
)
```

**Create file `db.sqlite3` if not present already.**

---

### Common Field Types

| Field Type        | Description                      | Example                               |
| ----------------- | -------------------------------- | ------------------------------------- |
| `CharField`       | Short text (requires max_length) | `name = CharField(max_length=100)`    |
| `TextField`       | Long text                        | `bio = TextField()`                   |
| `IntegerField`    | Integer numbers                  | `age = IntegerField()`                |
| `FloatField`      | Decimal numbers                  | `price = FloatField()`                |
| `BooleanField`    | True/False                       | `active = BooleanField(default=True)` |
| `DateField`       | Date only                        | `birth_date = DateField()`            |
| `DateTimeField`   | Date and time                    | `created_at = DateTimeField()`        |
| `EmailField`      | Email validation                 | `email = EmailField()`                |
| `URLField`        | URL validation                   | `website = URLField()`                |
| `ForeignKey`      | Many-to-one relationship         | `author = ForeignKey(Author)`         |
| `ManyToManyField` | Many-to-many relationship        | `tags = ManyToManyField(Tag)`         |

---

### Field Options

| Option         | Description                          |
| -------------- | ------------------------------------ |
| `max_length`   | Maximum length for CharField         |
| `default`      | Default value for the field          |
| `null=True`    | Allow NULL in database               |
| `blank=True`   | Allow empty in forms                 |
| `unique=True`  | Enforce unique values                |
| `choices`      | Limit to specific choices            |
| `auto_now_add` | Set to current time on creation      |
| `auto_now`     | Update to current time on every save |

---

### Migrations

After defining/changing models, you need to create and apply migrations:

```bash
# Create migration files based on model changes
python manage.py makemigrations

# Apply migrations to database
python manage.py migrate
```

**What migrations do:**

1. Detect changes in your models
2. Generate SQL to update the database
3. Keep track of database schema versions
4. Allow reverting changes

**Open Django Shell**

```bash
python3 manage.py shell
```

**Create Book**

```py
from book_outlet.models import Book
harry_potter = Book(title="Harry Potter 1 – The Philosopher's Stone", rating=5)
harry_potter.save()
```

**Equivalent to:**

```sql
INSERT INTO books ( title, rating )
VALUES ('Lord of the Rings', 5)
```

```py
lord_of_the_rings = Book(title="Lord of the Rings", rating=4)
lord_of_the_rings.save()
```

**Read Book**

```py
Book.objects.all()
# <QuerySet [<Book: Book object (1)>, <Book: Book object (2)>]>
```

**Equivalent to:**

```sql
SELECT * FROM books;
```

---

**Add more attributes and str method**

```py
from django.core import validators
from django.db import models
from django.core.validators import MinValueValidator, MaxValueValidator

class Book(models.Model):
    title = models.CharField(max_length=50)
    rating = models.IntegerField(
        validators=[MinValueValidator(1), MaxValueValidator(5)])
    author = models.CharField(null=True, max_length=100)
    is_bestselling = models.BooleanField(default=False)

    def __str__(self):
        return f"{self.title} ({self.rating})"
```

**Migrate**

```bash
python manage.py makemigrations
python manage.py migrate

# Open Django Shell
python3 manage.py shell
```

**Read and Update**

```py
from book_outlet.models import Book

Book.objects.all()[1]
# <Book: Lord of the Rings (4)>

Book.objects.all()[1].author
Book.objects.all()[1].is_bestselling
# False

Book.objects.all()[1].rating
# 4

harry_potter = Book.objects.all()[0]
harry_potter.title
# "Harry Potter 1 – The Philosopher's Stone"

lotr = Book.objects.all()[1]
lotr.title
# 'Lord of the Rings'

harry_potter.author = "J.K. Rowling"
harry_potter.is_bestselling = True
harry_potter.save()

Book.objects.all()[0].author
# 'J.K. Rowling'

lotr.author = "J.R.R. Tolkien"
lotr.is_bestselling = True
lotr.save()

Book.objects.all()[1].author
# 'J.R.R. Tolkien'

Book.objects.all()[1].is_bestselling
# True
```

**Delete**

```py
harry_potter = Book.objects.all()[0]
harry_potter.delete()
# (1, {'book_outlet.Book': 1})
Book.objects.all()
# <QuerySet [<Book: Lord of the Rings (4)>]>
```

**create method**

```py
Book.objects.create(title="Harry Potter 1", rating=5, author="J.K. Rowling", is_bestselling=True)
# <Book: Harry Potter 1 (5)>
Book.objects.all()
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>]>
Book.objects.create(title="My Story", rating=2, author="Max", is_bestselling=False)
# <Book: My Story (2)>
Book.objects.create(title="Some random book", rating=1, author="Random Dude", is_bestselling=False)
# <Book: Some random book (1)>
Book.objects.all()
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>, <Book: My Story (2)>, <Book: Some random book (1)>]>
```

**get method**

```py
Book.objects.get(title="My Story")
# <Book: My Story (2)>
Book.objects.get(rating=5)
# <Book: Harry Potter 1 (5)>
Book.objects.all()
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>, <Book: My Story (2)>, <Book: Some random book (1)>]>
Book.objects.get(is_bestselling=True)
# Traceback (most recent call last):
#   ...
# django.core.exceptions.MultipleObjectsReturned: get() returned more than one Book -- it returned 2!
```

**filter method**

```py
Book.objects.filter(is_bestselling=True)
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>]>
Book.objects.filter(is_bestselling=False)
# <QuerySet [<Book: My Story (2)>, <Book: Some random book (1)>]>
Book.objects.filter(is_bestselling=False, rating=2)
# <QuerySet [<Book: My Story (2)>]>
Book.objects.filter(rating<3)
# Traceback (most recent call last):
#   ...
# NameError: name 'rating' is not defined
Book.objects.filter(rating__lt=3)
# <QuerySet [<Book: My Story (2)>, <Book: Some random book (1)>]>
Book.objects.filter(rating__lt=3, title__contains="Story")
# <QuerySet [<Book: My Story (2)>]>
```

**case-insensitive lookups and "OR" queries using Q objects**

```py
Book.objects.filter(rating__lt=3, title__contains="story")
# <QuerySet []>, but works for sqlite
Book.objects.filter(rating__lt=3, title__icontains="story")
# <QuerySet [<Book: My Story (2)>]>
from django.db.models import Q
Book.objects.filter(Q(rating__lt=3) | Q(is_bestselling=True)))
#   File "<console>", line 1
#     Book.objects.filter(Q(rating__lt=3) | Q(is_bestselling=True)))
#                                                                 ^
# SyntaxError: unmatched ')'
Book.objects.filter(Q(rating__lt=3) | Q(is_bestselling=True))
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>, <Book: My Story (2)>, <Book: Some random book (1)>]>

Book.objects.filter(Q(rating__lt=3) | Q(is_bestselling=True), Q(author="J.K. Rowling"))
# <QuerySet [<Book: Harry Potter 1 (5)>]>
Book.objects.filter(Q(rating__lt=3) | Q(is_bestselling=True), author="J.K. Rowling")
# <QuerySet [<Book: Harry Potter 1 (5)>]>
Book.objects.filter(author="J.K. Rowling", Q(rating__lt=3) | Q(is_bestselling=True))
#   File "<console>", line 1
#     Book.objects.filter(author="J.K. Rowling", Q(rating__lt=3) | Q(is_bestselling=True))
#                                              ^
# SyntaxError: positional argument follows keyword argument
```

**Query Optimizations**

```py
bestsellers = Book.objects.filter(is_bestselling=True)
amazing_bestsellers = bestsellers.filter(rating__gt=4)
print(bestsellers)
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>]>
print(amazing_bestsellers)
# <QuerySet [<Book: Harry Potter 1 (5)>]>
print(bestsellers)
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>]>

print(Book.objects.filter(rating__gt=3))
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>]>
print(Book.objects.filter(rating__gt=3))
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>]>
good_books = Book.objects.filter(rating__gt=3)
print(good_books)
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>]>
print(good_books)
# <QuerySet [<Book: Lord of the Rings (4)>, <Book: Harry Potter 1 (5)>]>
```

**Ordering and Aggregation**

```py
# Ordering
Book.objects.order_by('title')  # Ascending
Book.objects.order_by('-title')  # Descending
# Aggregation
Book.objects.aggregate(Avg('rating'))
Book.objects.aggregate(total=Count('id'))
```

---

### QuerySet Methods

| Method       | Description                                   |
| ------------ | --------------------------------------------- |
| `all()`      | Get all records                               |
| `get()`      | Get single record (raises error if not found) |
| `filter()`   | Get records matching conditions               |
| `exclude()`  | Get records NOT matching conditions           |
| `order_by()` | Sort records                                  |
| `first()`    | Get first record                              |
| `last()`     | Get last record                               |
| `count()`    | Count records                                 |
| `exists()`   | Check if records exist                        |
| `values()`   | Return dictionaries instead of objects        |
| `distinct()` | Remove duplicates                             |

---

### Lookup Expressions (Filters)

| Lookup       | Description               | Example                             |
| ------------ | ------------------------- | ----------------------------------- |
| `exact`      | Exact match               | `filter(name__exact='John')`        |
| `iexact`     | Case-insensitive exact    | `filter(name__iexact='john')`       |
| `contains`   | Contains substring        | `filter(title__contains='Python')`  |
| `icontains`  | Case-insensitive contains | `filter(title__icontains='python')` |
| `startswith` | Starts with               | `filter(name__startswith='J')`      |
| `endswith`   | Ends with                 | `filter(email__endswith='.com')`    |
| `gt`         | Greater than              | `filter(age__gt=18)`                |
| `gte`        | Greater than or equal     | `filter(age__gte=18)`               |
| `lt`         | Less than                 | `filter(price__lt=100)`             |
| `lte`        | Less than or equal        | `filter(price__lte=100)`            |
| `in`         | In a list                 | `filter(id__in=[1, 2, 3])`          |
| `isnull`     | Is NULL                   | `filter(bio__isnull=True)`          |

---

**Implementing Models in Django**

**Create book_outlet\templates\book_outlet\base.html**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{% block title %}{% endblock title %}</title>
  </head>
  <body>
    {% block content %} {% endblock content %}
  </body>
</html>
```

**Create book_outlet\templates\book_outlet\index.html**

```html
<pre>
{% extends "book_outlet/base.html" %}

{% block title %}
  All Books
{% endblock title %}

{% block content %}
  <ul>
    <li>Book 1...</li>
  </ul>
{% endblock content %}
</pre>
```

**Register url**

```py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", include("book_outlet.urls"))
]
```

```py
from django.urls import path

from . import views

urlpatterns = [
    path("", views.index)
]
```

**Create view**

```py
from django.shortcuts import render

def index(request):
  return render(request, "book_outlet/index.html")
```

**Update view**

```py
from django.shortcuts import render

from .models import Book

def index(request):
  books = Book.objects.all()
  return render(request, "book_outlet/index.html", {
    "books": books
  })
```

**Update book_outlet\templates\book_outlet\index.html**

```html
<pre>
{% extends "book_outlet/base.html" %}

{% block title %}
  All Books
{% endblock title %}

{% block content %}
  <ul>
    {% for book in books %}
      <li>{{ book.title }} (Rating: {{ book.rating }})</li>
    {% endfor %}
  </ul>
{% endblock content %}
</pre>
```

---

**Create book_outlet\templates\book_outlet\book_detail.html**

```html
<pre>
{% extends "book_outlet/base.html" %}

{% block title %}
  {{ title }}
{% endblock %}

{% block content %}
  <h1>{{ title }}</h1>
  <h2>{{ author }}</h2>
  <p>The book has a rating of {{ rating }} 
  {% if is_bestseller %}
    and is a bestseller.
  {% else %}
    but isn't a bestseller.
  {% endif %}
  </p>
{% endblock %}
</pre>
```

**Add Book detail url**

```py
from django.urls import path

from . import views

urlpatterns = [
    path("", views.index),
    path("<int:id>", views.book_detail, name="book-detail")
]
```

**Add Book detail view**

```py
from django.shortcuts import get_object_or_404, render
from django.http import Http404


def book_detail(request, id):
  # try:
  #   book = Book.objects.get(pk=id)
  # except:
  #   raise Http404()
  book = get_object_or_404(Book, pk=id)
  return render(request, "book_outlet/book_detail.html", {
    "title": book.title,
    "author": book.author,
    "rating": book.rating,
    "is_bestseller": book.is_bestselling
  })
```

**Update index page**

```html
<pre>
{% extends "book_outlet/base.html" %}

{% block title %}
  All Books
{% endblock %}

{% block content %}
  <ul>
    {% for book in books %}
      <li>
        <a href="{% url 'book-detail' book.id %}">
          {{ book.title }}
        </a>
        (Rating: {{ book.rating }})
      </li>
    {% endfor %}
  </ul>
{% endblock %}
</pre>
```

---

**Using slug instead of id**

**Update model**

```py
from django.core import validators
from django.db import models
from django.core.validators import MinValueValidator, MaxValueValidator
from django.urls import reverse
from django.utils.text import slugify


class Book(models.Model):
    title = models.CharField(max_length=50)
    rating = models.IntegerField(
        validators=[MinValueValidator(1), MaxValueValidator(5)])
    author = models.CharField(null=True, max_length=100)
    is_bestselling = models.BooleanField(default=False)
    # Harry Potter 1 => harry-potter-1
    slug = models.SlugField(default="", null=False, db_index=True)

    def get_absolute_url(self):
        return reverse("book-detail", args=[self.slug])
        # return reverse("book-detail", args=[self.id])

    def save(self, *args, **kwargs):
        self.slug = slugify(self.title)
        super().save(*args, **kwargs)

    def __str__(self):
        return f"{self.title} ({self.rating})"
```

**Just call save again to add slug**

```py
Book.objects.get(title="Harry Potter 1").save()
Book.objects.get(title="Harry Potter 1").slug
# 'harry-potter-1'
Book.objects.get(title="Lord of the Rings").save()
Book.objects.get(title="Lord of the Rings").slug
# 'lord-of-the-rings'
Book.objects.get(title="My Story").save()
Book.objects.get(title="Some random book").save()
```

**Update urls**

```py
from django.urls import path

from . import views

urlpatterns = [
    path("", views.index),
    path("<slug:slug>", views.book_detail, name="book-detail")
]
```

**Update view**

```py
def book_detail(request, slug):
  book = get_object_or_404(Book, slug=slug)
  return render(request, "book_outlet/book_detail.html", {
    "title": book.title,
    "author": book.author,
    "rating": book.rating,
    "is_bestseller": book.is_bestselling
  })
```

**Update index page**

```html
<pre>
{% extends "book_outlet/base.html" %}

{% block title %}
  All Books
{% endblock %}

{% block content %}
  <ul>
    {% for book in books %}
      <li><a href="{{ book.get_absolute_url }}">{{ book.title }}</a> (Rating: {{ book.rating }})</li>
    {% endfor %}
  </ul>
{% endblock %}
</pre>
```

---

**Adding Summary**

**Update view**

```py
from django.db.models import Avg

def index(request):
  books = Book.objects.all().order_by("-rating")
  num_books = books.count()
  avg_rating = books.aggregate(Avg("rating")) # rating__avg, rating__min

  return render(request, "book_outlet/index.html", {
    "books": books,
    "total_number_of_books": num_books,
    "average_rating": avg_rating
  })
```

**Update index page**

```html
<pre>
{% extends "book_outlet/base.html" %}

{% block title %}
  All Books
{% endblock %}

{% block content %}
  <ul>
    {% for book in books %}
      <li><a href="{{ book.get_absolute_url }}">{{ book.title }}</a> (Rating: {{ book.rating }})</li>
    {% endfor %}
  </ul>

  <hr>

  <p>Total Number Of Books: {{ total_number_of_books }}</p>
  <p>Average Rating: {{ average_rating.rating__avg }}</p>
{% endblock %}
</pre>
```

---

---

---

## Module 5: Project Blog - Basics

### 5.1 Module Introduction

**Objective:** Build a blog application's frontend structure.

---

### 5.2 Setting Up the Starting Project

```bash
django-admin startproject my_blog
cd my_blog
python manage.py startapp blog
```

**Register in settings.py:**

```python
INSTALLED_APPS = [
    # ...
    'blog',
]
```

---

### 5.3 Planning the Project

**Pages Needed:**

1. **Home Page** - Featured posts
2. **All Posts** - List of all blog posts
3. **Single Post** - Individual post detail
4. **About** - About page

**Data Structure:**

- Title, Author, Date, Image, Excerpt, Content, Slug

---

### 5.4 Adding URLs & Views

```python
# blog/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.starting_page, name='starting-page'),
    path('posts/', views.posts, name='posts-page'),
    path('posts/<slug:slug>/', views.post_detail, name='post-detail-page'),
]

# blog/views.py
def starting_page(request):
    return render(request, 'blog/index.html')

def posts(request):
    return render(request, 'blog/all-posts.html')

def post_detail(request, slug):
    return render(request, 'blog/post-detail.html')
```

---

### 5.5 Adding First Templates

**Template Structure:**

```
blog/
└── templates/
    └── blog/
        ├── base.html
        ├── index.html
        ├── all-posts.html
        └── post-detail.html
```

---

### 5.6 Template Content & Static Files

**base.html:**

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{% block title %}My Blog{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'blog/css/base.css' %}" />
    {% block css %}{% endblock %}
  </head>
  <body>
    <header>
      <nav>
        <a href="{% url 'starting-page' %}">Home</a>
        <a href="{% url 'posts-page' %}">All Posts</a>
      </nav>
    </header>
    <main>{% block content %}{% endblock %}</main>
  </body>
</html>
```

---

### 5.7 Adding Dummy Data to Views

```python
# blog/views.py
all_posts = [
    {
        'slug': 'first-post',
        'title': 'My First Post',
        'author': 'John Doe',
        'date': date(2024, 1, 15),
        'image': 'post1.jpg',
        'excerpt': 'This is my first blog post...',
        'content': 'Full content here...'
    },
    # More posts...
]

def starting_page(request):
    latest_posts = sorted(all_posts, key=lambda x: x['date'], reverse=True)[:3]
    return render(request, 'blog/index.html', {'posts': latest_posts})

def posts(request):
    return render(request, 'blog/all-posts.html', {'posts': all_posts})

def post_detail(request, slug):
    post = next((p for p in all_posts if p['slug'] == slug), None)
    if not post:
        raise Http404()
    return render(request, 'blog/post-detail.html', {'post': post})
```

---

### 5.8 Adding a 404 Page

```python
# views.py
from django.http import Http404

def post_detail(request, slug):
    post = get_post_by_slug(slug)
    if not post:
        raise Http404("Post not found")
    return render(request, 'blog/post-detail.html', {'post': post})
```

---

## Module 6: Data & Models

### 6.1 Module Introduction

**Objective:** Master Django's ORM and database operations.

---

### 6.2 Understanding Database Options

**Supported Databases:**

- SQLite (default, file-based)
- PostgreSQL (recommended for production)
- MySQL
- Oracle

---

### 6.3 Django Models

**Models Define:**

- Database table structure
- Field types and constraints
- Relationships between tables

---

### 6.4 Creating a Django Model

```python
# blog/models.py
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=150)
    excerpt = models.CharField(max_length=300)
    content = models.TextField()
    date = models.DateField(auto_now_add=True)
    slug = models.SlugField(unique=True)

    def __str__(self):
        return self.title
```

**Common Field Types:**
| Field | Description |
|-------|-------------|
| CharField | Short text with max_length |
| TextField | Long text |
| IntegerField | Integer numbers |
| FloatField | Decimal numbers |
| BooleanField | True/False |
| DateField | Date |
| DateTimeField | Date and time |
| EmailField | Email validation |
| SlugField | URL-friendly text |
| ImageField | Image upload |
| FileField | File upload |
| ForeignKey | Many-to-one relationship |

---

### 6.5 Migrations

```bash
# Create migration files
python manage.py makemigrations

# Apply migrations to database
python manage.py migrate

# View migration SQL
python manage.py sqlmigrate blog 0001
```

---

### 6.6 Inserting Data

```python
# Django shell
python manage.py shell

>>> from blog.models import Post
>>> post = Post(title="Hello", excerpt="...", content="...", slug="hello")
>>> post.save()

# Or using create()
>>> Post.objects.create(title="Hello", excerpt="...", content="...", slug="hello")
```

---

### 6.7 Getting All Entries

```python
>>> Post.objects.all()  # QuerySet of all posts
>>> list(Post.objects.all())  # Evaluate QuerySet
```

---

### 6.8 Updating Data

```python
>>> post = Post.objects.get(id=1)
>>> post.title = "Updated Title"
>>> post.save()

# Bulk update
>>> Post.objects.filter(author='John').update(author='Jane')
```

---

### 6.9 Deleting Data

```python
>>> post = Post.objects.get(id=1)
>>> post.delete()

# Bulk delete
>>> Post.objects.filter(date__lt=date(2020, 1, 1)).delete()
```

---

### 6.10 Querying & Filtering Data

```python
# Get single object
>>> Post.objects.get(slug='hello')

# Filter (returns QuerySet)
>>> Post.objects.filter(author='John')
>>> Post.objects.filter(title__contains='Django')
>>> Post.objects.filter(date__year=2024)

# Exclude
>>> Post.objects.exclude(author='John')

# Chaining
>>> Post.objects.filter(author='John').exclude(date__year=2020)
```

**Field Lookups:**

- `__exact`, `__iexact` - Exact match
- `__contains`, `__icontains` - Contains
- `__startswith`, `__endswith` - String matching
- `__gt`, `__gte`, `__lt`, `__lte` - Comparisons
- `__in` - In list
- `__isnull` - Is null check

---

### 6.11 "or" Conditions

```python
from django.db.models import Q

# OR condition
Post.objects.filter(Q(author='John') | Q(author='Jane'))

# Complex queries
Post.objects.filter(
    Q(author='John') & (Q(title__contains='Django') | Q(title__contains='Python'))
)
```

---

### 6.12 Aggregation & Ordering

```python
from django.db.models import Count, Avg, Sum, Max, Min

# Ordering
Post.objects.order_by('date')  # Ascending
Post.objects.order_by('-date')  # Descending

# Aggregation
Post.objects.aggregate(Avg('rating'))
Post.objects.aggregate(total=Count('id'))
```

---

## Module 7: Admin

### 7.1 Module Introduction

**Objective:** Configure Django's admin panel.

---

### 7.2 Logging Into Admin

```bash
# Create superuser
python manage.py createsuperuser

# Access at http://127.0.0.1:8000/admin/
```

---

### 7.3 Adding Models to Admin

```python
# blog/admin.py
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```

---

### 7.4 Configuring Model Fields

```python
# blog/admin.py
from django.contrib import admin
from .models import Post

class PostAdmin(admin.ModelAdmin):
    list_display = ('title', 'date', 'author')
    list_filter = ('date', 'author')
    search_fields = ('title', 'content')
    prepopulated_fields = {'slug': ('title',)}
    ordering = ('-date',)

admin.site.register(Post, PostAdmin)
```

---

### 7.5 More Config Options

```python
class PostAdmin(admin.ModelAdmin):
    list_display = ('title', 'date', 'author')
    list_filter = ('date', 'author')
    search_fields = ('title', 'content')
    prepopulated_fields = {'slug': ('title',)}
    date_hierarchy = 'date'
    list_editable = ('author',)
    list_per_page = 20
    fieldsets = (
        ('Basic Info', {
            'fields': ('title', 'slug', 'author')
        }),
        ('Content', {
            'fields': ('excerpt', 'content', 'image')
        }),
    )
```

---

## Module 8: Relationships

### 8.1 Module Introduction

**Objective:** Understand database relationships in Django.

---

### 8.2 Understanding Relationship Types

**Three Types:**

1. **One-to-Many (ForeignKey)** - Author → Many Posts
2. **One-to-One (OneToOneField)** - User → Profile
3. **Many-to-Many (ManyToManyField)** - Posts ↔ Tags

---

### 8.3 One-to-Many Relationship

```python
# models.py
class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    email = models.EmailField()

class Post(models.Model):
    title = models.CharField(max_length=150)
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name='posts')
    # ...
```

**on_delete Options:**

- `CASCADE` - Delete related objects
- `PROTECT` - Prevent deletion
- `SET_NULL` - Set to NULL (requires null=True)
- `SET_DEFAULT` - Set to default value

**Usage:**

```python
>>> author = Author.objects.get(id=1)
>>> author.posts.all()  # All posts by author

>>> post = Post.objects.get(id=1)
>>> post.author  # Get author of post
```

---

### 8.4 One-to-One Relationship

```python
class Address(models.Model):
    street = models.CharField(max_length=200)
    city = models.CharField(max_length=100)

class Author(models.Model):
    name = models.CharField(max_length=100)
    address = models.OneToOneField(Address, on_delete=models.CASCADE)
```

---

### 8.5 Many-to-Many Relationship

```python
class Tag(models.Model):
    caption = models.CharField(max_length=50)

class Post(models.Model):
    title = models.CharField(max_length=150)
    tags = models.ManyToManyField(Tag, related_name='posts')
```

**Usage:**

```python
>>> post = Post.objects.get(id=1)
>>> post.tags.all()
>>> post.tags.add(tag1, tag2)
>>> post.tags.remove(tag1)
>>> post.tags.clear()

>>> tag = Tag.objects.get(id=1)
>>> tag.posts.all()  # All posts with this tag
```

---

### 8.6 Cross Model Queries

```python
# Get posts by author name
Post.objects.filter(author__first_name='John')

# Get posts with specific tag
Post.objects.filter(tags__caption='Django')
```

---

## Module 9: Forms

### 9.1 Module Introduction

**Objective:** Handle user input through forms.

---

### 9.2 Get & Post Requests

```python
def contact(request):
    if request.method == 'POST':
        # Handle form submission
        pass
    return render(request, 'contact.html')
```

---

### 9.3 CSRF Protection

```html
<form method="POST">
  {% csrf_token %}
  <input type="text" name="username" />
  <button type="submit">Submit</button>
</form>
```

---

### 9.4 Using Django Form Class

```python
# forms.py
from django import forms

class ReviewForm(forms.Form):
    username = forms.CharField(max_length=100)
    email = forms.EmailField()
    rating = forms.IntegerField(min_value=1, max_value=5)
    review = forms.CharField(widget=forms.Textarea)

# views.py
def review(request):
    if request.method == 'POST':
        form = ReviewForm(request.POST)
        if form.is_valid():
            # Process data
            username = form.cleaned_data['username']
            # ...
            return redirect('success')
    else:
        form = ReviewForm()
    return render(request, 'review.html', {'form': form})
```

---

### 9.5 Rendering Forms in Templates

```html
<!-- Method 1: Auto-render all fields -->
<form method="POST">
  {% csrf_token %} {{ form }}
  <button type="submit">Submit</button>
</form>

<!-- Method 2: Render individually -->
<form method="POST">
  {% csrf_token %} {{ form.username.label_tag }} {{ form.username }} {{
  form.username.errors }}
  <!-- ... -->
</form>

<!-- Method 3: As list/table -->
{{ form.as_p }} {{ form.as_ul }} {{ form.as_table }}
```

---

### 9.6 ModelForms

```python
# forms.py
from django.forms import ModelForm
from .models import Review

class ReviewForm(ModelForm):
    class Meta:
        model = Review
        fields = ['username', 'email', 'rating', 'review']
        # or fields = '__all__'
        # exclude = ['created_date']

        widgets = {
            'review': forms.Textarea(attrs={'rows': 4}),
        }
        labels = {
            'username': 'Your Name',
        }

# views.py
def create_review(request):
    if request.method == 'POST':
        form = ReviewForm(request.POST)
        if form.is_valid():
            form.save()  # Automatically saves to database
            return redirect('success')
    else:
        form = ReviewForm()
    return render(request, 'review.html', {'form': form})
```

---

## Module 10: Class-Based Views

### 10.1 Module Introduction

**Objective:** Use class-based views for cleaner code.

---

### 10.2 TemplateView

```python
from django.views.generic import TemplateView

class HomeView(TemplateView):
    template_name = 'home.html'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['title'] = 'Welcome'
        return context

# urls.py
path('', HomeView.as_view(), name='home'),
```

---

### 10.3 ListView

```python
from django.views.generic import ListView
from .models import Post

class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'
    context_object_name = 'posts'
    ordering = ['-date']
    paginate_by = 10
```

---

### 10.4 DetailView

```python
from django.views.generic import DetailView

class PostDetailView(DetailView):
    model = Post
    template_name = 'posts/detail.html'
    context_object_name = 'post'
    slug_field = 'slug'
    slug_url_kwarg = 'slug'
```

---

### 10.5 FormView

```python
from django.views.generic.edit import FormView
from .forms import ContactForm

class ContactView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = '/success/'

    def form_valid(self, form):
        # Process the form
        return super().form_valid(form)
```

---

### 10.6 CreateView

```python
from django.views.generic.edit import CreateView
from .models import Post

class PostCreateView(CreateView):
    model = Post
    fields = ['title', 'excerpt', 'content']
    template_name = 'posts/create.html'
    success_url = '/posts/'
```

## Module 11: File Upload

### 11.1 Module Introduction

**Objective:** Handle file and image uploads in Django.

---

### 11.2 Setting Up Media Files

**settings.py:**

```python
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'uploads'
```

**urls.py (development only):**

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... your urls
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

### 11.3 Adding FileField to Model

```python
# models.py
class Document(models.Model):
    title = models.CharField(max_length=200)
    file = models.FileField(upload_to='documents/')
    uploaded_at = models.DateTimeField(auto_now_add=True)
```

---

### 11.4 Using ImageField

```bash
# Install Pillow for image handling
pip install Pillow
```

```python
class Post(models.Model):
    title = models.CharField(max_length=200)
    image = models.ImageField(upload_to='posts/', blank=True, null=True)
```

---

### 11.5 File Upload Form

```python
# forms.py
from django import forms

class DocumentForm(forms.Form):
    title = forms.CharField(max_length=200)
    file = forms.FileField()

# views.py
def upload(request):
    if request.method == 'POST':
        form = DocumentForm(request.POST, request.FILES)
        if form.is_valid():
            # Handle file
            uploaded_file = request.FILES['file']
            # Save logic...
            return redirect('success')
    else:
        form = DocumentForm()
    return render(request, 'upload.html', {'form': form})
```

**Template:**

```html
<form method="POST" enctype="multipart/form-data">
  {% csrf_token %} {{ form }}
  <button type="submit">Upload</button>
</form>
```

---

### 11.6 CreateView for File Upload

```python
from django.views.generic.edit import CreateView
from .models import Document

class DocumentCreateView(CreateView):
    model = Document
    fields = ['title', 'file']
    template_name = 'upload.html'
    success_url = '/documents/'
```

---

### 11.7 Displaying Uploaded Files

```html
{% if document.file %}
<a href="{{ document.file.url }}">Download File</a>
{% endif %} {% if post.image %}
<img src="{{ post.image.url }}" alt="{{ post.title }}" />
{% endif %}
```

---

## Module 12: Sessions

### 12.1 Module Introduction

**Objective:** Manage user sessions and temporary data storage.

---

### 12.2 What are Sessions?

**Sessions allow you to:**

- Store data per visitor
- Persist data across requests
- Track user activity without authentication

---

### 12.3 Enabling Sessions

**settings.py (enabled by default):**

```python
INSTALLED_APPS = [
    # ...
    'django.contrib.sessions',
]

MIDDLEWARE = [
    # ...
    'django.contrib.sessions.middleware.SessionMiddleware',
]

# Session settings
SESSION_ENGINE = 'django.contrib.sessions.backends.db'  # Database-backed
SESSION_COOKIE_AGE = 1209600  # 2 weeks in seconds
```

---

### 12.4 Storing Data in Sessions

```python
def add_to_cart(request, product_id):
    cart = request.session.get('cart', [])
    cart.append(product_id)
    request.session['cart'] = cart
    return redirect('cart')
```

---

### 12.5 Using Session Data

```python
def view_cart(request):
    cart = request.session.get('cart', [])
    # Get products from cart
    products = Product.objects.filter(id__in=cart)
    return render(request, 'cart.html', {'products': products})

def clear_cart(request):
    if 'cart' in request.session:
        del request.session['cart']
    return redirect('cart')
```

---

### 12.6 Safely Accessing Session Data

```python
# Safe access with default
cart = request.session.get('cart', [])
username = request.session.get('username', 'Guest')

# Check if key exists
if 'cart' in request.session:
    # ...

# Set with modification flag
request.session['cart'] = ['item1', 'item2']
request.session.modified = True  # Explicitly mark as modified
```

---

## Module 13: Project Blog - Forms, Files & Sessions

### 13.1 Module Introduction

**Objective:** Complete the blog project with forms, file uploads, and sessions.

---

### 13.2 Adding ImageField to Post Model

```python
# blog/models.py
class Post(models.Model):
    title = models.CharField(max_length=150)
    excerpt = models.CharField(max_length=300)
    content = models.TextField()
    image = models.ImageField(upload_to='posts/', null=True, blank=True)
    date = models.DateField(auto_now_add=True)
    slug = models.SlugField(unique=True)
    author = models.ForeignKey(Author, on_delete=models.SET_NULL, null=True)
    tags = models.ManyToManyField(Tag)
```

---

### 13.3 Adding Comment Model

```python
class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    user_name = models.CharField(max_length=120)
    user_email = models.EmailField()
    text = models.TextField(max_length=400)
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ['-created_at']
```

---

### 13.4 Adding Comment Form

```python
# forms.py
from django import forms
from .models import Comment

class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['user_name', 'user_email', 'text']
        widgets = {
            'user_name': forms.TextInput(attrs={'placeholder': 'Your Name'}),
            'user_email': forms.EmailInput(attrs={'placeholder': 'Your Email'}),
            'text': forms.Textarea(attrs={'placeholder': 'Your Comment', 'rows': 4}),
        }
        labels = {
            'user_name': 'Name',
            'user_email': 'Email',
            'text': 'Comment',
        }
```

---

### 13.5 Handling Comment Submission

```python
# views.py
from .forms import CommentForm

class PostDetailView(View):
    def get(self, request, slug):
        post = get_object_or_404(Post, slug=slug)
        form = CommentForm()
        comments = post.comments.all()
        return render(request, 'blog/post-detail.html', {
            'post': post,
            'form': form,
            'comments': comments
        })

    def post(self, request, slug):
        post = get_object_or_404(Post, slug=slug)
        form = CommentForm(request.POST)

        if form.is_valid():
            comment = form.save(commit=False)
            comment.post = post
            comment.save()
            return redirect('post-detail-page', slug=slug)

        comments = post.comments.all()
        return render(request, 'blog/post-detail.html', {
            'post': post,
            'form': form,
            'comments': comments
        })
```

---

### 13.6 Displaying Comments

```html
<!-- post-detail.html -->
<section class="comments">
  <h2>Comments ({{ comments|length }})</h2>

  {% for comment in comments %}
  <article class="comment">
    <header>
      <strong>{{ comment.user_name }}</strong>
      <time>{{ comment.created_at|date:"M d, Y" }}</time>
    </header>
    <p>{{ comment.text }}</p>
  </article>
  {% empty %}
  <p>No comments yet. Be the first to comment!</p>
  {% endfor %}
</section>

<section class="comment-form">
  <h2>Leave a Comment</h2>
  <form method="POST">
    {% csrf_token %} {{ form.as_p }}
    <button type="submit">Post Comment</button>
  </form>
</section>
```

---

### 13.7 Read Later Feature with Sessions

```python
# views.py
class ReadLaterView(View):
    def get(self, request):
        stored_posts = request.session.get('read_later', [])
        posts = Post.objects.filter(id__in=stored_posts)
        return render(request, 'blog/read-later.html', {'posts': posts})

    def post(self, request):
        post_id = request.POST.get('post_id')
        stored_posts = request.session.get('read_later', [])

        if post_id not in stored_posts:
            stored_posts.append(post_id)
        else:
            stored_posts.remove(post_id)

        request.session['read_later'] = stored_posts
        return redirect(request.META.get('HTTP_REFERER', 'starting-page'))
```

**Template Button:**

```html
<form method="POST" action="{% url 'read-later' %}">
  {% csrf_token %}
  <input type="hidden" name="post_id" value="{{ post.id }}" />
  {% if post.id|stringformat:'i' in stored_posts %}
  <button type="submit">Remove from Read Later</button>
  {% else %}
  <button type="submit">Read Later</button>
  {% endif %}
</form>
```

---

## Module 14: Deployment

### 14.1 Module Introduction

**Objective:** Deploy Django application to production.

---

### 14.2 Deployment Considerations

**Key Areas:**

1. Database (PostgreSQL for production)
2. Static files (CDN or S3)
3. Media files (S3 or cloud storage)
4. Security settings
5. Environment variables

---

### 14.3 Production Settings

**settings.py:**

```python
import os
from pathlib import Path

# Security
DEBUG = os.environ.get('DEBUG', 'False') == 'True'
SECRET_KEY = os.environ.get('SECRET_KEY')
ALLOWED_HOSTS = os.environ.get('ALLOWED_HOSTS', '').split(',')

# HTTPS settings
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
```

---

### 14.4 Database Configuration

```python
# settings.py
import dj_database_url

DATABASES = {
    'default': dj_database_url.config(
        default=os.environ.get('DATABASE_URL')
    )
}

# Or manual PostgreSQL config
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('DB_NAME'),
        'USER': os.environ.get('DB_USER'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST'),
        'PORT': '5432',
    }
}
```

---

### 14.5 Collecting Static Files

```bash
# settings.py
STATIC_ROOT = BASE_DIR / 'staticfiles'

# Collect all static files
python manage.py collectstatic
```

---

### 14.6 Locking Dependencies

```bash
# Generate requirements file
pip freeze > requirements.txt

# Or use pipenv
pipenv lock

# Or use poetry
poetry export -f requirements.txt > requirements.txt
```

**requirements.txt example:**

```
Django==4.2
gunicorn==21.2.0
psycopg2-binary==2.9.9
whitenoise==6.6.0
dj-database-url==2.1.0
boto3==1.34.0
django-storages==1.14.2
```

---

### 14.7 Using Environment Variables

```python
# settings.py
import os
from dotenv import load_dotenv

load_dotenv()  # Load from .env file

SECRET_KEY = os.environ.get('SECRET_KEY')
DEBUG = os.environ.get('DEBUG', 'False') == 'True'
DATABASE_URL = os.environ.get('DATABASE_URL')
```

**.env file (don't commit!):**

```
SECRET_KEY=your-super-secret-key
DEBUG=False
DATABASE_URL=postgres://user:pass@host:5432/dbname
AWS_ACCESS_KEY_ID=your-key
AWS_SECRET_ACCESS_KEY=your-secret
```

---

### 14.8 Serving Static Files with WhiteNoise

```bash
pip install whitenoise
```

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',  # Add after SecurityMiddleware
    # ... other middleware
]

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

### 14.9 Serving Files via S3

```bash
pip install django-storages boto3
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'storages',
]

# AWS Settings
AWS_ACCESS_KEY_ID = os.environ.get('AWS_ACCESS_KEY_ID')
AWS_SECRET_ACCESS_KEY = os.environ.get('AWS_SECRET_ACCESS_KEY')
AWS_STORAGE_BUCKET_NAME = os.environ.get('AWS_BUCKET_NAME')
AWS_S3_REGION_NAME = 'us-east-1'
AWS_S3_FILE_OVERWRITE = False
AWS_DEFAULT_ACL = None

# Static files on S3
STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'

# Media files on S3
DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
```

---

### 14.10 Deploying with Gunicorn

```bash
pip install gunicorn
```

**Procfile (for Heroku/Railway):**

```
web: gunicorn myproject.wsgi:application
```

**Run locally:**

```bash
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
```

---

### 14.11 Docker Deployment

**Dockerfile:**

```dockerfile
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN python manage.py collectstatic --noinput

EXPOSE 8000

CMD ["gunicorn", "myproject.wsgi:application", "--bind", "0.0.0.0:8000"]
```

**docker-compose.yml:**

```yaml
version: "3.8"

services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DEBUG=False
      - DATABASE_URL=postgres://user:pass@db:5432/dbname
    depends_on:
      - db

  db:
    image: postgres:15
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=dbname
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass

volumes:
  postgres_data:
```

---

### 14.12 Deployment Checklist

```bash
# Run Django's deployment check
python manage.py check --deploy
```

**Security Checklist:**

- [ ] DEBUG = False
- [ ] SECRET_KEY from environment variable
- [ ] ALLOWED_HOSTS configured
- [ ] HTTPS enabled
- [ ] Database credentials secured
- [ ] Static files collected
- [ ] Migrations run
- [ ] Admin URL changed (optional)

---

## Quick Reference

### Essential Commands

```bash
# Project Setup
django-admin startproject projectname
python manage.py startapp appname

# Database
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser

# Development
python manage.py runserver
python manage.py shell

# Static Files
python manage.py collectstatic

# Testing
python manage.py test

# Deployment
python manage.py check --deploy
```

### Common Imports

```python
# Views
from django.shortcuts import render, redirect, get_object_or_404
from django.http import HttpResponse, HttpResponseRedirect, Http404
from django.urls import reverse
from django.views import View
from django.views.generic import TemplateView, ListView, DetailView, CreateView

# Models
from django.db import models
from django.db.models import Q, Count, Avg, Sum

# Forms
from django import forms
from django.forms import ModelForm

# Auth
from django.contrib.auth.decorators import login_required
from django.contrib.auth.mixins import LoginRequiredMixin
```
