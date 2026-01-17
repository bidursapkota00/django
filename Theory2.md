# Backend Web Development with Django - Complete Notes

## Syllabus

- 3.1 MVC architecture in web development
- 3.2 Role of backend in web applications
- 3.3 Backend web framework: Django
- 3.4 Handling requests and responses
- 3.5 Form data handling and sessions
- 3.6 Routing, middleware, templating concepts
- 3.7 Overview and comparison of backend frameworks
- 3.8 Database integration: Relational versus NoSQL, CRUD operations, ORM concept
- 3.9 Authentication and authorization: Cookies, sessions, JWT
- 3.10 Middleware for logging, error handling, and security

---

# 3.1 MVC Architecture in Web Development

## What is MVC?

**MVC (Model-View-Controller)** is a software architectural pattern that separates an application into three interconnected components. This separation helps organize code, making it easier to develop, test, and maintain applications.

## The Three Components

### 1. Model (Data Layer)

The **Model** represents the data and business logic of the application. It is responsible for:

- **Data Management**: Storing, retrieving, updating, and deleting data
- **Business Rules**: Implementing the core logic and rules of the application
- **Data Validation**: Ensuring data integrity before storage
- **Database Interaction**: Communicating with databases

**Key Characteristics:**

- Independent of user interface
- Contains no presentation logic
- Notifies the View of data changes
- Can be reused across different interfaces

### 2. View (Presentation Layer)

The **View** is responsible for displaying data to the user. It handles:

- **User Interface**: Rendering HTML, CSS, and visual elements
- **Data Presentation**: Displaying information from the Model
- **User Input Display**: Showing forms, buttons, and interactive elements
- **Output Formatting**: Converting data into user-friendly formats

**Key Characteristics:**

- Receives data from the Controller
- Contains no business logic
- Only handles presentation concerns
- Can have multiple views for the same data

### 3. Controller (Logic Layer)

The **Controller** acts as an intermediary between Model and View. It manages:

- **Request Handling**: Receiving and processing user input
- **Flow Control**: Determining which View to display
- **Data Coordination**: Fetching data from Model and passing to View
- **Business Logic Orchestration**: Coordinating between components

**Key Characteristics:**

- Receives user requests
- Processes input and makes decisions
- Updates Model based on user actions
- Selects appropriate View for response

## MVC Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         USER                                     │
│                          │                                       │
│                    (1) Request                                   │
│                          ▼                                       │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    CONTROLLER                            │    │
│  │    • Receives user request                               │    │
│  │    • Processes input                                     │    │
│  │    • Coordinates Model and View                          │    │
│  └─────────────────────────────────────────────────────────┘    │
│           │                                    │                 │
│     (2) Query/Update                    (4) Send Data           │
│           ▼                                    ▼                 │
│  ┌──────────────────┐              ┌──────────────────┐         │
│  │      MODEL       │              │       VIEW       │         │
│  │                  │ (3) Data     │                  │         │
│  │  • Data logic    │─────────────▶│  • Presentation  │         │
│  │  • Database      │              │  • HTML/CSS      │         │
│  │  • Validation    │              │  • Templates     │         │
│  └──────────────────┘              └──────────────────┘         │
│                                            │                     │
│                                      (5) Response                │
│                                            ▼                     │
│                                         USER                     │
└─────────────────────────────────────────────────────────────────┘
```

## Request-Response Flow

1. **User Action**: User clicks a link or submits a form
2. **Controller Receives**: Controller handles the incoming request
3. **Model Interaction**: Controller asks Model for data or updates it
4. **Data Preparation**: Model returns requested data to Controller
5. **View Selection**: Controller chooses appropriate View and sends data
6. **Response Rendering**: View renders HTML with the data
7. **User Sees Result**: Browser displays the response to user

## Responsibilities Summary

| Component  | Primary Responsibility          | Example                      |
| ---------- | ------------------------------- | ---------------------------- |
| Model      | Data + Business Logic           | User class, database queries |
| View       | Presentation                    | HTML templates, forms        |
| Controller | Request handling + Coordination | URL routing, form processing |

## Benefits of MVC

1. **Separation of Concerns**: Each component has a specific job
2. **Code Reusability**: Models and Views can be reused
3. **Parallel Development**: Teams can work on different components
4. **Easier Testing**: Components can be tested independently
5. **Maintainability**: Changes in one component don't affect others
6. **Scalability**: Easy to extend functionality

## MVC in Real-World Analogy

Think of a **restaurant**:

- **Model** = Kitchen (prepares food, manages recipes)
- **View** = Dining Area (presents food beautifully to customers)
- **Controller** = Waiter (takes orders, communicates between kitchen and customer)

---

# 3.2 Role of Backend in Web Applications

## What is Backend?

The **backend** (also called server-side) is the part of a web application that users don't see. It runs on servers and handles the logic, database operations, authentication, and everything that happens "behind the scenes."

## Frontend vs Backend

| Aspect     | Frontend                   | Backend                    |
| ---------- | -------------------------- | -------------------------- |
| Location   | Browser (client-side)      | Server (server-side)       |
| Languages  | HTML, CSS, JavaScript      | Python, Java, PHP, Node.js |
| Visibility | User sees it               | Hidden from user           |
| Purpose    | User interface             | Data processing, logic     |
| Examples   | Buttons, forms, animations | Database, authentication   |

## Core Responsibilities of Backend

### 1. Server Logic

The backend contains the core **application logic** that processes requests:

- **Request Processing**: Receiving HTTP requests from clients
- **Response Generation**: Creating appropriate responses (HTML, JSON, etc.)
- **Computation**: Performing calculations and data transformations
- **Integration**: Connecting with external services and APIs

**How it works:**

```
Client Request → Server Receives → Process Logic → Generate Response → Send Back
```

### 2. Routing

**Routing** determines how an application responds to different URLs:

- **URL Mapping**: Connecting URLs to specific functions
- **HTTP Methods**: Handling GET, POST, PUT, DELETE requests
- **Parameter Extraction**: Getting data from URL paths and query strings
- **Navigation Control**: Directing users to correct pages

**Example URL Patterns:**

```
/home          → Show homepage
/users         → List all users
/users/5       → Show user with ID 5
/products/add  → Add new product form
```

### 3. Business Logic

**Business logic** implements the rules and operations specific to your application:

- **Data Validation**: Checking if input data is correct
- **Calculations**: Computing prices, discounts, statistics
- **Workflow Management**: Controlling step-by-step processes
- **Rule Enforcement**: Ensuring business rules are followed

**Examples:**

- E-commerce: Calculate total price with tax and discount
- Banking: Check if account has sufficient balance
- Social Media: Determine who can see a post

### 4. Security Layers

Backend implements multiple **security measures** to protect the application:

#### Authentication

Verifying "Who are you?"

- Login systems (username/password)
- Token-based authentication
- OAuth (Login with Google/Facebook)

#### Authorization

Determining "What can you do?"

- Role-based access (Admin, User, Guest)
- Permission systems
- Resource ownership

#### Data Protection

Securing stored and transmitted data:

- Password hashing (never store plain passwords)
- HTTPS encryption
- Input sanitization (prevent SQL injection)
- CSRF protection

#### Common Security Threats:

| Threat        | Description            | Prevention                     |
| ------------- | ---------------------- | ------------------------------ |
| SQL Injection | Malicious SQL in input | Use ORM, parameterized queries |
| XSS           | Injecting scripts      | Escape output, sanitize input  |
| CSRF          | Forged requests        | CSRF tokens                    |
| Brute Force   | Password guessing      | Rate limiting, captcha         |

## Backend Architecture Diagram

```
┌────────────────────────────────────────────────────────────────┐
│                        CLIENT (Browser)                         │
│                              │                                  │
│                         HTTP Request                            │
│                              ▼                                  │
├────────────────────────────────────────────────────────────────┤
│                         WEB SERVER                              │
│                    (Nginx, Apache, etc.)                        │
│                              │                                  │
├────────────────────────────────────────────────────────────────┤
│                      APPLICATION SERVER                         │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                      ROUTING LAYER                        │  │
│  │              (Maps URLs to Functions)                     │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              │                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                    MIDDLEWARE LAYER                       │  │
│  │     (Authentication, Logging, Security Checks)            │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              │                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                    BUSINESS LOGIC                         │  │
│  │        (Controllers, Services, Validators)                │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              │                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                      DATA LAYER                           │  │
│  │              (Models, ORM, Queries)                       │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              │                                  │
├────────────────────────────────────────────────────────────────┤
│                         DATABASE                                │
│                  (MySQL, PostgreSQL, MongoDB)                   │
└────────────────────────────────────────────────────────────────┘
```

## What Backend Developers Do

1. **Design APIs**: Create endpoints for frontend to communicate
2. **Database Design**: Structure how data is stored
3. **Write Business Logic**: Implement application features
4. **Security Implementation**: Protect against attacks
5. **Performance Optimization**: Make applications fast
6. **Server Management**: Deploy and maintain servers

---

# 3.3 Backend Web Framework: Django

## What is Django?

**Django** is a high-level Python web framework that enables rapid development of secure and maintainable websites. Created in 2005, it follows the "batteries included" philosophy, providing most tools needed for web development out of the box.

## Why Django?

| Feature             | Benefit                                      |
| ------------------- | -------------------------------------------- |
| Rapid Development   | Build applications quickly                   |
| Secure by Default   | Protection against common attacks            |
| Scalable            | Handles high traffic (Instagram uses Django) |
| Versatile           | Suitable for any type of website             |
| Great Documentation | Easy to learn and reference                  |
| Large Community     | Lots of packages and support                 |

## MTV Pattern (Django's Version of MVC)

Django uses **MTV (Model-Template-View)** which is similar to MVC:

| MVC        | Django MTV   | Purpose                             |
| ---------- | ------------ | ----------------------------------- |
| Model      | **Model**    | Data and database                   |
| View       | **Template** | HTML presentation                   |
| Controller | **View**     | Business logic and request handling |

### Django MTV Explained:

1. **Model**: Defines data structure, interacts with database
2. **Template**: HTML files with Django template language
3. **View**: Python functions/classes that handle requests and return responses

```
┌─────────────────────────────────────────────────────────────┐
│                      Django MTV Flow                         │
│                                                              │
│   User Request                                               │
│        │                                                     │
│        ▼                                                     │
│   ┌─────────┐    ┌─────────┐    ┌──────────┐                │
│   │  URLs   │───▶│  VIEW   │───▶│ TEMPLATE │                │
│   │(routing)│    │ (logic) │    │  (HTML)  │                │
│   └─────────┘    └────┬────┘    └──────────┘                │
│                       │              │                       │
│                       ▼              │                       │
│                  ┌─────────┐         │                       │
│                  │  MODEL  │         │                       │
│                  │ (data)  │         │                       │
│                  └────┬────┘         │                       │
│                       │              │                       │
│                       ▼              │                       │
│                  ┌─────────┐         │                       │
│                  │DATABASE │         │                       │
│                  └─────────┘         ▼                       │
│                              HTTP Response to User           │
└─────────────────────────────────────────────────────────────┘
```

## Django Project Structure

When you create a Django project, you get this structure:

```
myproject/                  # Project root directory
│
├── manage.py              # Command-line utility for Django
│
├── myproject/             # Project configuration package
│   ├── __init__.py        # Makes this a Python package
│   ├── settings.py        # Project settings (database, apps, etc.)
│   ├── urls.py            # Main URL routing
│   ├── asgi.py            # ASGI configuration (async)
│   └── wsgi.py            # WSGI configuration (deployment)
│
└── myapp/                 # Your application
    ├── __init__.py        # Makes this a Python package
    ├── admin.py           # Admin panel configuration
    ├── apps.py            # App configuration
    ├── models.py          # Database models
    ├── views.py           # View functions/classes
    ├── urls.py            # App-specific URLs (you create this)
    ├── forms.py           # Form definitions (you create this)
    ├── tests.py           # Test cases
    ├── migrations/        # Database migrations
    │   └── __init__.py
    └── templates/         # HTML templates (you create this)
        └── myapp/
            └── index.html
```

## Key Files Explained

### settings.py

Contains all project configurations:

- Database settings
- Installed apps list
- Middleware configuration
- Template settings
- Static files configuration
- Security settings

### urls.py

Maps URLs to views:

- Defines URL patterns
- Connects URLs to view functions
- Includes app-specific URLs

### models.py

Defines database structure:

- Python classes representing database tables
- Field definitions (CharField, IntegerField, etc.)
- Relationships between models

### views.py

Contains business logic:

- Functions or classes handling requests
- Processes data from models
- Returns responses (HTML, JSON, redirect)

## Django Apps

A Django **app** is a Python package that provides a specific functionality. A project can contain multiple apps.

**Examples of Apps:**

- `users` - User management
- `blog` - Blog posts and comments
- `shop` - E-commerce functionality
- `api` - REST API endpoints

**Benefits of Apps:**

1. **Modularity**: Each app handles one feature
2. **Reusability**: Apps can be used in different projects
3. **Organization**: Code is logically grouped
4. **Team Collaboration**: Different teams can work on different apps

## Creating a Django Project - Minimal Example

```python
# 1. Install Django
# pip install django

# 2. Create project
# django-admin startproject mysite

# 3. Create app
# python manage.py startapp blog

# 4. In blog/views.py
from django.http import HttpResponse

def home(request):
    return HttpResponse("Hello, Django!")

# 5. In blog/urls.py (create this file)
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
]

# 6. In mysite/urls.py
from django.urls import path, include

urlpatterns = [
    path('', include('blog.urls')),
]

# 7. Run server
# python manage.py runserver
```

---

# 3.4 Handling Requests and Responses

## HTTP Request-Response Cycle

Every web interaction follows this cycle:

```
┌────────────┐         HTTP Request          ┌────────────┐
│            │ ─────────────────────────────▶ │            │
│   CLIENT   │                                │   SERVER   │
│  (Browser) │ ◀───────────────────────────── │  (Django)  │
│            │         HTTP Response          │            │
└────────────┘                                └────────────┘
```

## HTTP Methods

| Method     | Purpose                | Example Use         |
| ---------- | ---------------------- | ------------------- |
| **GET**    | Retrieve data          | Loading a webpage   |
| **POST**   | Submit data            | Form submission     |
| **PUT**    | Update entire resource | Update user profile |
| **PATCH**  | Partial update         | Change only email   |
| **DELETE** | Remove resource        | Delete a post       |

## Django Views

Views are Python functions or classes that receive web requests and return web responses.

### Function-Based Views (FBV)

Simple and straightforward approach:

```python
# views.py
from django.http import HttpResponse

def hello(request):
    return HttpResponse("Hello, World!")

def greet(request, name):
    return HttpResponse(f"Hello, {name}!")
```

### Class-Based Views (CBV)

More structured, reusable approach:

```python
# views.py
from django.views import View
from django.http import HttpResponse

class HelloView(View):
    def get(self, request):
        return HttpResponse("Hello from GET!")

    def post(self, request):
        return HttpResponse("Hello from POST!")
```

## The Request Object

Django automatically creates a `request` object containing all request information:

| Attribute         | Description                   |
| ----------------- | ----------------------------- |
| `request.method`  | HTTP method (GET, POST, etc.) |
| `request.GET`     | Dictionary of GET parameters  |
| `request.POST`    | Dictionary of POST data       |
| `request.path`    | URL path                      |
| `request.user`    | Current logged-in user        |
| `request.session` | Session data dictionary       |
| `request.COOKIES` | Dictionary of cookies         |
| `request.FILES`   | Uploaded files                |

```python
def my_view(request):
    method = request.method        # 'GET' or 'POST'
    name = request.GET.get('name') # Get URL parameter
    user = request.user            # Current user
    return HttpResponse(f"Method: {method}, Name: {name}")
```

## Response Types

### 1. HttpResponse - Basic Response

```python
from django.http import HttpResponse

def simple_response(request):
    return HttpResponse("Plain text response")

def html_response(request):
    return HttpResponse("<h1>HTML Response</h1>")
```

### 2. JsonResponse - JSON Data

```python
from django.http import JsonResponse

def json_response(request):
    data = {
        'name': 'John',
        'age': 25,
        'city': 'New York'
    }
    return JsonResponse(data)

# For lists, use safe=False
def json_list(request):
    items = ['apple', 'banana', 'cherry']
    return JsonResponse(items, safe=False)
```

### 3. render() - HTML Templates

```python
from django.shortcuts import render

def template_response(request):
    context = {'name': 'John', 'items': [1, 2, 3]}
    return render(request, 'myapp/home.html', context)
```

### 4. redirect() - Redirect to Another URL

```python
from django.shortcuts import redirect

def redirect_view(request):
    return redirect('home')  # Redirect to named URL
    # or
    return redirect('/other-page/')  # Redirect to path
```

## Handling Different HTTP Methods

```python
from django.http import HttpResponse, JsonResponse

def handle_methods(request):
    if request.method == 'GET':
        return HttpResponse("This is GET request")

    elif request.method == 'POST':
        data = request.POST
        return HttpResponse("Data received via POST")

    else:
        return HttpResponse("Method not allowed", status=405)
```

## URL Parameters

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('user/<int:user_id>/', views.user_detail),
    path('article/<slug:article_slug>/', views.article),
]

# views.py
def user_detail(request, user_id):
    return HttpResponse(f"User ID: {user_id}")

def article(request, article_slug):
    return HttpResponse(f"Article: {article_slug}")
```

## Complete Example

```python
# views.py
from django.http import JsonResponse
from django.shortcuts import render

def api_view(request):
    if request.method == 'GET':
        data = {'message': 'GET request', 'status': 'success'}
        return JsonResponse(data)

    elif request.method == 'POST':
        name = request.POST.get('name', 'Guest')
        data = {'message': f'Hello, {name}', 'status': 'created'}
        return JsonResponse(data, status=201)
```

---

# 3.5 Form Data Handling and Sessions

## Form Processing in Django

Forms are essential for collecting user input. Django provides robust form handling.

## HTML Forms Basics

```html
<form method="POST" action="/submit/">
  {% csrf_token %}
  <input type="text" name="username" />
  <input type="email" name="email" />
  <button type="submit">Submit</button>
</form>
```

## Processing POST Data

```python
# views.py
from django.shortcuts import render, redirect
from django.http import HttpResponse

def contact_form(request):
    if request.method == 'POST':
        name = request.POST.get('name')
        email = request.POST.get('email')
        message = request.POST.get('message')

        # Process the data (save to database, send email, etc.)
        print(f"Received: {name}, {email}, {message}")

        return redirect('success')

    return render(request, 'contact.html')
```

## Django Forms (The Better Way)

Django Forms provide validation and security:

```python
# forms.py
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)

# views.py
from .forms import ContactForm

def contact(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']
            # Process valid data
            return redirect('success')
    else:
        form = ContactForm()

    return render(request, 'contact.html', {'form': form})
```

## CSRF Protection

**CSRF (Cross-Site Request Forgery)** is an attack where a malicious site tricks users into performing unwanted actions.

### How CSRF Works:

1. User logs into your site (has valid session)
2. User visits malicious site
3. Malicious site sends hidden request to your site
4. Your site thinks it's a legitimate request

### Django's CSRF Protection:

- Django generates a unique token for each session
- Token must be included in all POST requests
- Server validates token before processing

```html
<!-- Always include csrf_token in forms -->
<form method="POST">
  {% csrf_token %}
  <!-- form fields -->
</form>
```

```python
# CSRF is enabled by default in settings.py
MIDDLEWARE = [
    'django.middleware.csrf.CsrfViewMiddleware',  # CSRF middleware
    # ... other middleware
]
```

## Sessions

**Sessions** allow you to store data between requests for each user.

### How Sessions Work:

```
┌─────────────────────────────────────────────────────────────┐
│                    SESSION MECHANISM                         │
│                                                              │
│  1. User visits site                                         │
│        │                                                     │
│        ▼                                                     │
│  2. Server creates session, generates Session ID             │
│        │                                                     │
│        ▼                                                     │
│  3. Session ID sent to browser as cookie                     │
│        │                                                     │
│        ▼                                                     │
│  4. Browser sends Session ID with every request              │
│        │                                                     │
│        ▼                                                     │
│  5. Server looks up session data using Session ID            │
└─────────────────────────────────────────────────────────────┘
```

### Using Sessions in Django:

```python
# views.py
def set_session(request):
    # Store data in session
    request.session['username'] = 'john'
    request.session['cart'] = ['item1', 'item2']
    return HttpResponse("Session data set!")

def get_session(request):
    # Retrieve data from session
    username = request.session.get('username', 'Guest')
    cart = request.session.get('cart', [])
    return HttpResponse(f"Hello, {username}! Cart: {cart}")

def clear_session(request):
    # Remove specific key
    del request.session['username']
    # Or clear all session data
    request.session.flush()
    return HttpResponse("Session cleared!")
```

### Session Configuration:

```python
# settings.py

# Session engine (where to store session data)
SESSION_ENGINE = 'django.contrib.sessions.backends.db'  # Database (default)
# SESSION_ENGINE = 'django.contrib.sessions.backends.cache'  # Cache
# SESSION_ENGINE = 'django.contrib.sessions.backends.file'   # File

# Session expiry
SESSION_COOKIE_AGE = 1209600  # 2 weeks in seconds

# Session cookie settings
SESSION_COOKIE_SECURE = True   # Only send over HTTPS
SESSION_COOKIE_HTTPONLY = True # Not accessible via JavaScript
```

## Cookies vs Sessions

| Aspect     | Cookies          | Sessions          |
| ---------- | ---------------- | ----------------- |
| Storage    | Client (browser) | Server            |
| Size Limit | ~4KB             | Unlimited         |
| Security   | Less secure      | More secure       |
| Data Type  | Strings only     | Any Python object |
| Expiry     | Set explicitly   | Server-controlled |

---

# 3.6 Routing, Middleware, Templating Concepts

## URL Routing (URL Dispatcher)

Routing maps URLs to views. Django uses `urlpatterns` in `urls.py`.

### Basic URL Patterns:

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('about/', views.about, name='about'),
    path('contact/', views.contact, name='contact'),
]
```

### URL Parameters:

```python
# Path converters
urlpatterns = [
    path('user/<int:id>/', views.user),        # Integer: /user/5/
    path('post/<slug:slug>/', views.post),     # Slug: /post/my-post/
    path('file/<path:filepath>/', views.file), # Path: /file/docs/readme.txt
    path('item/<str:name>/', views.item),      # String: /item/apple/
]
```

### Including App URLs:

```python
# project/urls.py
from django.urls import path, include

urlpatterns = [
    path('blog/', include('blog.urls')),
    path('shop/', include('shop.urls')),
]
```

### Named URLs:

```python
# urls.py
path('profile/<int:id>/', views.profile, name='user_profile')

# In templates
<a href="{% url 'user_profile' id=5 %}">View Profile</a>

# In views
from django.urls import reverse
url = reverse('user_profile', kwargs={'id': 5})
```

## Middleware

**Middleware** is code that runs before/after every request/response. It's like a series of filters.

### Request/Response Flow with Middleware:

```
Request → Middleware 1 → Middleware 2 → View → Middleware 2 → Middleware 1 → Response
             ↓              ↓            ↓            ↑              ↑
          process        process     process      process        process
          request        request      view       response       response
```

### Built-in Middleware:

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',     # Security headers
    'django.contrib.sessions.middleware.SessionMiddleware',  # Sessions
    'django.middleware.common.CommonMiddleware',         # URL normalization
    'django.middleware.csrf.CsrfViewMiddleware',         # CSRF protection
    'django.contrib.auth.middleware.AuthenticationMiddleware',  # Auth
    'django.contrib.messages.middleware.MessageMiddleware',     # Messages
]
```

### Custom Middleware:

```python
# middleware.py
class SimpleMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Code before view (request phase)
        print(f"Request: {request.path}")

        response = self.get_response(request)

        # Code after view (response phase)
        print(f"Response status: {response.status_code}")

        return response
```

## Django Template Language

Templates are HTML files with Django's template syntax.

### Template Location:

```python
# settings.py
TEMPLATES = [
    {
        'DIRS': [BASE_DIR / 'templates'],  # Project templates
        'APP_DIRS': True,  # Look in app/templates/ folders
    },
]
```

### Basic Template Syntax:

```html
<!-- templates/base.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>{% block title %}My Site{% endblock %}</title>
  </head>
  <body>
    {% block content %}{% endblock %}
  </body>
</html>
```

### Variables:

```html
<!-- Display variables -->
<h1>Hello, {{ name }}!</h1>
<p>Email: {{ user.email }}</p>
```

### Filters:

```html
{{ name|upper }}
<!-- JOHN -->
{{ name|lower }}
<!-- john -->
{{ name|title }}
<!-- John Doe -->
{{ text|truncatewords:10 }}
<!-- First 10 words -->
{{ date|date:"Y-m-d" }}
<!-- 2024-01-15 -->
{{ price|floatformat:2 }}
<!-- 19.99 -->
```

### Tags:

```html
<!-- If statement -->
{% if user.is_authenticated %}
<p>Welcome, {{ user.username }}!</p>
{% else %}
<p>Please log in.</p>
{% endif %}

<!-- For loop -->
{% for item in items %}
<li>{{ item.name }}</li>
{% empty %}
<li>No items found.</li>
{% endfor %}

<!-- Include another template -->
{% include 'partials/navbar.html' %}
```

### Template Inheritance:

```html
<!-- base.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>{% block title %}{% endblock %}</title>
  </head>
  <body>
    <nav>Navigation here</nav>
    {% block content %}{% endblock %}
    <footer>Footer here</footer>
  </body>
</html>

<!-- home.html -->
{% extends 'base.html' %} {% block title %}Home Page{% endblock %} {% block
content %}
<h1>Welcome Home!</h1>
{% endblock %}
```

---

# 3.7 Overview and Comparison of Backend Frameworks

## Popular Backend Frameworks

### 1. Django (Python)

**Philosophy:** "Batteries included" - comes with everything built-in

**Strengths:**

- Complete framework with admin panel, ORM, authentication
- Excellent security features
- Great documentation
- Large ecosystem

**Best For:** Large applications, content management, e-commerce

**Used By:** Instagram, Pinterest, Disqus

### 2. Flask (Python)

**Philosophy:** "Micro-framework" - minimal core, add what you need

**Strengths:**

- Lightweight and flexible
- Easy to learn
- Great for APIs
- No forced structure

**Best For:** Small to medium projects, APIs, prototypes

### 3. FastAPI (Python)

**Philosophy:** Fast, modern, automatic documentation

**Strengths:**

- Extremely fast performance
- Automatic API documentation (Swagger/OpenAPI)
- Built-in data validation
- Async support

**Best For:** Modern APIs, microservices, high-performance applications

### 4. ASP.NET Core (C#)

**Philosophy:** Enterprise-grade, Microsoft ecosystem

**Strengths:**

- High performance
- Strong typing
- Excellent tooling (Visual Studio)
- Cross-platform

**Best For:** Enterprise applications, Windows ecosystem

### 5. Ruby on Rails (Ruby)

**Philosophy:** "Convention over Configuration"

**Strengths:**

- Rapid development
- Strong community
- Mature ecosystem
- Easy to read code

**Best For:** Startups, MVPs, content sites

**Used By:** GitHub, Shopify, Airbnb

### 6. Spring Boot (Java)

**Philosophy:** Enterprise Java made easy

**Strengths:**

- Robust and scalable
- Strong enterprise support
- Excellent for microservices
- Vast ecosystem

**Best For:** Large enterprise applications, banking, healthcare

### 7. Node.js/Express (JavaScript)

**Philosophy:** JavaScript everywhere (frontend + backend)

**Strengths:**

- Same language for frontend and backend
- Non-blocking I/O
- Huge npm ecosystem
- Real-time applications

**Best For:** Real-time apps, APIs, full-stack JavaScript

## Comparison Table

| Feature           | Django    | Flask       | FastAPI   | ASP.NET    | Rails    | Spring     | Express    |
| ----------------- | --------- | ----------- | --------- | ---------- | -------- | ---------- | ---------- |
| Language          | Python    | Python      | Python    | C#         | Ruby     | Java       | JavaScript |
| Learning Curve    | Medium    | Easy        | Easy      | Medium     | Easy     | Hard       | Easy       |
| Performance       | Good      | Good        | Excellent | Excellent  | Good     | Excellent  | Good       |
| Built-in Features | Many      | Few         | Medium    | Many       | Many     | Many       | Few        |
| ORM               | Yes       | No          | No        | Yes        | Yes      | Yes        | No         |
| Admin Panel       | Yes       | No          | No        | No         | No       | No         | No         |
| Best For          | Full apps | APIs, Small | APIs      | Enterprise | Startups | Enterprise | Real-time  |

## When to Choose What?

| Scenario                | Recommended Framework  |
| ----------------------- | ---------------------- |
| Quick prototype         | Flask, Rails           |
| REST API                | FastAPI, Express       |
| Large web app           | Django, Rails          |
| Enterprise app          | Spring Boot, ASP.NET   |
| Real-time features      | Express, FastAPI       |
| Full-stack JS team      | Express                |
| Data science background | Django, Flask, FastAPI |

---

# 3.8 Database Integration: ORM and CRUD Operations

## Relational vs NoSQL Databases

### Relational Databases (SQL)

**Structure:** Tables with rows and columns, strict schema

**Examples:** PostgreSQL, MySQL, SQLite

**Characteristics:**

- Fixed schema (must define structure first)
- ACID compliance (reliable transactions)
- Relationships via foreign keys
- SQL query language

**Best For:** Structured data, complex queries, transactions

### NoSQL Databases

**Structure:** Flexible, various data models

**Examples:** MongoDB, Redis, Cassandra

**Types:**

- **Document:** MongoDB (JSON-like documents)
- **Key-Value:** Redis (simple key-value pairs)
- **Column-family:** Cassandra (columns grouped together)
- **Graph:** Neo4j (nodes and relationships)

**Best For:** Unstructured data, high scalability, rapid development

### Comparison

| Aspect         | SQL                      | NoSQL                     |
| -------------- | ------------------------ | ------------------------- |
| Schema         | Fixed, predefined        | Flexible, dynamic         |
| Scaling        | Vertical (bigger server) | Horizontal (more servers) |
| Relationships  | Foreign keys             | Embedded or references    |
| Transactions   | Strong ACID              | Eventually consistent     |
| Query Language | SQL                      | Various                   |
| Best For       | Complex queries          | Simple, scalable data     |

## ORM (Object-Relational Mapping)

**ORM** maps database tables to Python classes, allowing you to interact with the database using Python code instead of SQL.

### Benefits of ORM:

1. **No SQL needed** - Write Python, not SQL
2. **Database agnostic** - Switch databases easily
3. **Security** - Prevents SQL injection
4. **Productivity** - Faster development

### Django ORM Concept:

```
┌─────────────────┐         ┌─────────────────┐
│  Python Class   │ ◀─────▶ │  Database Table │
│                 │   ORM   │                 │
│  class User:    │         │   users table   │
│    name         │         │   name column   │
│    email        │         │   email column  │
└─────────────────┘         └─────────────────┘
```

## Django Models

Models define your database structure:

```python
# models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    published = models.DateField()
    in_stock = models.BooleanField(default=True)

    def __str__(self):
        return self.title
```

### Common Field Types:

| Field Type    | Python Type | Description       |
| ------------- | ----------- | ----------------- |
| CharField     | str         | Short text        |
| TextField     | str         | Long text         |
| IntegerField  | int         | Integer           |
| DecimalField  | Decimal     | Precise decimal   |
| BooleanField  | bool        | True/False        |
| DateField     | date        | Date              |
| DateTimeField | datetime    | Date and time     |
| EmailField    | str         | Email (validated) |
| ForeignKey    | object      | Relationship      |

## CRUD Operations

CRUD = **C**reate, **R**ead, **U**pdate, **D**elete

### Create

```python
# Method 1: Create and save
book = Book(title="Python 101", author="John", price=29.99)
book.save()

# Method 2: Create directly
book = Book.objects.create(
    title="Django Guide",
    author="Jane",
    price=39.99
)
```

### Read (Query)

```python
# Get all books
books = Book.objects.all()

# Get one book by ID
book = Book.objects.get(id=1)

# Filter books
cheap_books = Book.objects.filter(price__lt=30)
python_books = Book.objects.filter(title__contains="Python")

# Get first/last
first_book = Book.objects.first()
last_book = Book.objects.last()

# Count
total = Book.objects.count()
```

### Update

```python
# Method 1: Get, modify, save
book = Book.objects.get(id=1)
book.price = 24.99
book.save()

# Method 2: Update directly
Book.objects.filter(id=1).update(price=24.99)

# Update multiple
Book.objects.filter(author="John").update(in_stock=False)
```

### Delete

```python
# Delete one
book = Book.objects.get(id=1)
book.delete()

# Delete with filter
Book.objects.filter(price__lt=10).delete()

# Delete all (be careful!)
Book.objects.all().delete()
```

## Query Operators

```python
# Comparison
Book.objects.filter(price__gt=20)    # Greater than
Book.objects.filter(price__lt=20)    # Less than
Book.objects.filter(price__gte=20)   # Greater than or equal
Book.objects.filter(price__lte=20)   # Less than or equal

# Text matching
Book.objects.filter(title__contains="Python")    # Contains
Book.objects.filter(title__icontains="python")   # Case-insensitive
Book.objects.filter(title__startswith="The")     # Starts with
Book.objects.filter(title__endswith="Guide")     # Ends with

# In list
Book.objects.filter(id__in=[1, 2, 3])

# Null check
Book.objects.filter(author__isnull=True)

# Ordering
Book.objects.order_by('price')       # Ascending
Book.objects.order_by('-price')      # Descending
```

## Database Migrations

Migrations sync your models with the database:

```bash
# Create migration files
python manage.py makemigrations

# Apply migrations to database
python manage.py migrate
```

---

# 3.9 Authentication and Authorization

## Authentication vs Authorization

| Authentication    | Authorization      |
| ----------------- | ------------------ |
| "Who are you?"    | "What can you do?" |
| Verifies identity | Checks permissions |
| Login process     | Access control     |
| Username/password | Roles/permissions  |

## How Login Works

```
┌─────────────────────────────────────────────────────────────┐
│                     LOGIN FLOW                               │
│                                                              │
│  1. User enters credentials                                  │
│        │                                                     │
│        ▼                                                     │
│  2. Server validates credentials                             │
│        │                                                     │
│     ┌──┴──┐                                                 │
│     │Valid?│                                                 │
│     └──┬──┘                                                 │
│    Yes │  No                                                 │
│        ▼   ▼                                                │
│  3a. Create    3b. Error                                    │
│      Session       Message                                   │
│        │                                                     │
│        ▼                                                     │
│  4. Send Session ID (cookie)                                │
│        │                                                     │
│        ▼                                                     │
│  5. User is logged in                                        │
└─────────────────────────────────────────────────────────────┘
```

## Cookies

**Cookies** are small pieces of data stored in the browser.

### Characteristics:

- Sent with every HTTP request to the domain
- Have expiration dates
- Can be secure (HTTPS only)
- Size limit ~4KB

### Setting Cookies in Django:

```python
from django.http import HttpResponse

def set_cookie(request):
    response = HttpResponse("Cookie set!")
    response.set_cookie('username', 'john', max_age=3600)  # 1 hour
    return response

def get_cookie(request):
    username = request.COOKIES.get('username', 'Guest')
    return HttpResponse(f"Hello, {username}")

def delete_cookie(request):
    response = HttpResponse("Cookie deleted!")
    response.delete_cookie('username')
    return response
```

## Sessions

**Sessions** store user data on the server side.

### Sessions vs Cookies

```
┌─────────────────────────────────────────────────────────────┐
│                                                              │
│   COOKIES                        SESSIONS                    │
│   ────────                       ────────                    │
│   ┌─────────┐                    ┌─────────┐                │
│   │ Browser │                    │ Browser │                │
│   │         │                    │         │                │
│   │ Cookie: │                    │ Cookie: │                │
│   │ user=   │                    │ session │                │
│   │ john    │                    │ id=abc  │                │
│   │ age=25  │                    │         │                │
│   └─────────┘                    └────┬────┘                │
│        │                              │                      │
│        │                              ▼                      │
│   All data                       ┌─────────┐                │
│   in browser                     │ Server  │                │
│   (visible)                      │         │                │
│                                  │ Session │                │
│                                  │ abc:    │                │
│                                  │ user=   │                │
│                                  │ john    │                │
│                                  └─────────┘                │
│                                  Data on server             │
│                                  (hidden)                   │
└─────────────────────────────────────────────────────────────┘
```

### Session in Django:

```python
def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        # Validate user...
        request.session['user_id'] = user.id
        request.session['username'] = username
        return redirect('home')

def profile_view(request):
    if 'user_id' not in request.session:
        return redirect('login')

    username = request.session.get('username')
    return HttpResponse(f"Welcome, {username}")

def logout_view(request):
    request.session.flush()  # Clear all session data
    return redirect('login')
```

## JWT (JSON Web Token)

**JWT** is a token-based authentication method, common in APIs.

### JWT Structure:

```
Header.Payload.Signature

Example:
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxfQ.abc123signature
```

### Parts:

1. **Header**: Algorithm and token type
2. **Payload**: User data (claims)
3. **Signature**: Verification signature

### JWT vs Sessions

| Aspect      | Sessions        | JWT               |
| ----------- | --------------- | ----------------- |
| Storage     | Server          | Client (token)    |
| Scalability | Harder          | Easier            |
| State       | Stateful        | Stateless         |
| Logout      | Easy            | Harder            |
| Best For    | Traditional web | APIs, mobile apps |

### JWT Flow:

```
┌─────────────────────────────────────────────────────────────┐
│                       JWT FLOW                               │
│                                                              │
│  1. User sends login credentials                             │
│        │                                                     │
│        ▼                                                     │
│  2. Server validates, creates JWT token                      │
│        │                                                     │
│        ▼                                                     │
│  3. Token sent to client (stored in localStorage)            │
│        │                                                     │
│        ▼                                                     │
│  4. Client sends token in Authorization header               │
│     Authorization: Bearer eyJhbG...                          │
│        │                                                     │
│        ▼                                                     │
│  5. Server validates token, grants access                    │
└─────────────────────────────────────────────────────────────┘
```

## Django Built-in Authentication

```python
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.decorators import login_required

def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        if user:
            login(request, user)
            return redirect('home')
    return render(request, 'login.html')

@login_required
def protected_view(request):
    return HttpResponse(f"Hello, {request.user.username}")

def logout_view(request):
    logout(request)
    return redirect('login')
```

---

# 3.10 Middleware for Logging, Error Handling, and Security

## Understanding Middleware

Middleware is a framework of hooks into Django's request/response processing. It's executed in order for requests and reverse order for responses.

```
Request:  User → MW1 → MW2 → MW3 → View
Response: User ← MW1 ← MW2 ← MW3 ← View
```

## Creating Custom Middleware

### Basic Structure:

```python
# middleware.py

class CustomMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        # One-time configuration

    def __call__(self, request):
        # Code executed before view

        response = self.get_response(request)

        # Code executed after view

        return response
```

### Registering Middleware:

```python
# settings.py
MIDDLEWARE = [
    # ... other middleware
    'myapp.middleware.CustomMiddleware',
]
```

## Logging Middleware

Track all requests to your application:

```python
# middleware.py
import logging
from datetime import datetime

logger = logging.getLogger(__name__)

class LoggingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Log request
        start_time = datetime.now()
        logger.info(f"Request: {request.method} {request.path}")

        response = self.get_response(request)

        # Log response
        duration = datetime.now() - start_time
        logger.info(f"Response: {response.status_code} ({duration.total_seconds():.2f}s)")

        return response
```

### Configure Logging in settings.py:

```python
# settings.py
LOGGING = {
    'version': 1,
    'handlers': {
        'file': {
            'class': 'logging.FileHandler',
            'filename': 'app.log',
        },
    },
    'loggers': {
        'myapp.middleware': {
            'handlers': ['file'],
            'level': 'INFO',
        },
    },
}
```

## Error Handling Middleware

Catch and handle errors gracefully:

```python
# middleware.py
import logging

logger = logging.getLogger(__name__)

class ErrorHandlingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        return self.get_response(request)

    def process_exception(self, request, exception):
        # Log the error
        logger.error(f"Error on {request.path}: {str(exception)}")

        # Return custom error response (optional)
        from django.http import JsonResponse
        return JsonResponse({
            'error': 'Something went wrong',
            'message': str(exception)
        }, status=500)
```

## Security Middleware

### Security Headers Middleware:

```python
# middleware.py

class SecurityHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)

        # Add security headers
        response['X-Content-Type-Options'] = 'nosniff'
        response['X-Frame-Options'] = 'DENY'
        response['X-XSS-Protection'] = '1; mode=block'

        return response
```

### IP Blocking Middleware:

```python
# middleware.py
from django.http import HttpResponseForbidden

class IPBlockMiddleware:
    BLOCKED_IPS = ['192.168.1.100', '10.0.0.50']

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        ip = request.META.get('REMOTE_ADDR')

        if ip in self.BLOCKED_IPS:
            return HttpResponseForbidden("Access Denied")

        return self.get_response(request)
```

## Complete Example: Combined Middleware

```python
# middleware.py
import logging
from datetime import datetime
from django.http import JsonResponse

logger = logging.getLogger(__name__)

class AppMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # 1. LOG REQUEST
        start = datetime.now()
        logger.info(f"[{request.method}] {request.path}")

        # 2. GET RESPONSE
        response = self.get_response(request)

        # 3. ADD SECURITY HEADERS
        response['X-Frame-Options'] = 'DENY'

        # 4. LOG RESPONSE
        duration = (datetime.now() - start).total_seconds()
        logger.info(f"Response: {response.status_code} in {duration:.2f}s")

        return response

    def process_exception(self, request, exception):
        # 5. HANDLE ERRORS
        logger.error(f"Error: {exception}")
        return JsonResponse({'error': str(exception)}, status=500)
```

## Summary of Middleware Uses

| Purpose            | What It Does                             |
| ------------------ | ---------------------------------------- |
| **Logging**        | Track requests, responses, timing        |
| **Error Handling** | Catch exceptions, return friendly errors |
| **Security**       | Add headers, block IPs, validate tokens  |
| **Authentication** | Verify user identity                     |
| **CORS**           | Handle cross-origin requests             |
| **Rate Limiting**  | Prevent abuse                            |
| **Caching**        | Store responses for performance          |

---

# Summary

This course covered the essential concepts of backend web development with Django:

1. **MVC Architecture** - Separation of concerns into Model, View, Controller
2. **Backend Role** - Server logic, routing, business logic, security
3. **Django Framework** - MTV pattern, project structure, apps
4. **Requests/Responses** - HTTP methods, views, JSON responses
5. **Forms and Sessions** - POST processing, CSRF protection, session management
6. **Routing and Templates** - URL dispatcher, middleware, template language
7. **Framework Comparison** - Django vs Flask vs FastAPI vs others
8. **Database Integration** - SQL vs NoSQL, ORM, CRUD operations
9. **Authentication** - Cookies, sessions, JWT
10. **Middleware** - Logging, error handling, security

These fundamentals will help you build robust, secure, and maintainable web applications!
