## Syllabus

- 3.1 MVC architecture in web development
  - Model–View–Controller pattern, flow diagrams, responsibilities.
- 3.2 Role of backend in web applications
  - Server logic, routing, business logic, security layers
- 3.3 Backend web framework: Django
  - Django apps, MTV pattern, project structure
- 3.4 Handling requests and responses
  - Writing views, HTTP methods, JSON response
- 3.5 Form data handling and sessions
  - Form POST processing, CSRF, session usage
- 3.6 Routing, middleware, templating concepts
  - URL dispatcher, custom middleware, Template language
- 3.7 Overview and comparison of backend frameworks
  - Django vs Flask vs FastAPI vs .NET MVC vs Rails vs Spring Boot vs Node.js
- 3.8 Database integration: Relational versus NoSQL, CRUD operations, ORM concept
  - Using Django ORM, basic models, performing CRUD.
- 3.9 Authentication and authorization: Cookies, sessions, JWT
  - Login flow, cookies vs sessions, intro to JWT.
- 3.10 Middleware for logging, error handling, and security
  - Build simple custom middleware for logging & error handling

---

# Unit 3: Backend Web Development

---

## 3.1 MVC Architecture in Web Development

### What is MVC?

**MVC (Model-View-Controller)** is a software architectural pattern that separates an application into three interconnected components. This separation helps organize code, makes applications easier to maintain, and allows multiple developers to work on different parts simultaneously.

MVC was originally developed for desktop applications but has become the foundation for most modern web frameworks. It promotes the principle of **"separation of concerns"** — each component has a specific job and should not interfere with the responsibilities of others.

---

### The Three Components of MVC

#### 1. Model (The Data Layer)

The **Model** represents the data and the business logic of the application. It is responsible for:

- **Data Storage**: Managing how data is stored, retrieved, and updated
- **Business Rules**: Enforcing the rules and logic of the application
- **Data Validation**: Ensuring data integrity before saving
- **Database Communication**: Interacting with databases to persist data

The Model is completely independent of the user interface. It doesn't know or care how data will be displayed — it only focuses on data management.

**Real-world analogy**: Think of the Model as the kitchen in a restaurant. The kitchen prepares and manages all the food (data), follows recipes (business rules), and stores ingredients (database). It doesn't interact with customers directly.

**Responsibilities of Model:**

- Define data structures (tables, fields, relationships)
- Validate data before saving
- Perform database queries
- Implement business logic (calculations, rules)
- Notify controllers/views when data changes

---

#### 2. View (The Presentation Layer)

The **View** is responsible for displaying data to the user. It is the visual representation of the Model's data and handles:

- **Data Display**: Presenting information in a user-friendly format
- **User Interface**: Rendering HTML, CSS, and visual elements
- **Templates**: Using template engines to generate dynamic content
- **No Business Logic**: Views should only display, never process data

The View receives data from the Controller and renders it. It should be passive and not directly access the Model.

**Real-world analogy**: The View is like the dining area and table setting in a restaurant. It presents the food (data) beautifully to customers but doesn't cook or prepare anything.

**Responsibilities of View:**

- Render HTML templates
- Display data from the Model
- Format data for presentation
- Handle visual layout and styling
- Generate responses (HTML, JSON, XML)

---

#### 3. Controller (The Logic Layer)

The **Controller** acts as an intermediary between the Model and View. It:

- **Handles User Input**: Receives requests from users
- **Processes Requests**: Interprets what the user wants to do
- **Coordinates Flow**: Decides which Model to use and which View to render
- **Updates Model**: Instructs the Model to update data
- **Selects View**: Chooses the appropriate View for the response

The Controller is the "traffic cop" of the application, directing the flow of data between components.

**Real-world analogy**: The Controller is like a waiter in a restaurant. The waiter takes orders from customers (user input), communicates with the kitchen (Model), and delivers the prepared food through proper presentation (View).

**Responsibilities of Controller:**

- Receive HTTP requests
- Parse and validate user input
- Call appropriate Model methods
- Pass data to Views
- Return HTTP responses

---

### MVC Flow Diagram

```
┌─────────────┐
│    USER     │
└──────┬──────┘
       │ 1. HTTP Request (clicks link, submits form)
       ▼
┌─────────────┐
│ CONTROLLER  │ ◄──── Receives and processes request
└──────┬──────┘
       │ 2. Request data from Model
       ▼
┌─────────────┐
│    MODEL    │ ◄──── Retrieves/modifies data
└──────┬──────┘
       │ 3. Return data to Controller
       ▼
┌─────────────┐
│ CONTROLLER  │ ◄──── Receives data, selects View
└──────┬──────┘
       │ 4. Pass data to View
       ▼
┌─────────────┐
│    VIEW     │ ◄──── Renders HTML with data
└──────┬──────┘
       │ 5. HTTP Response (HTML page)
       ▼
┌─────────────┐
│    USER     │ ◄──── Sees the rendered page
└─────────────┘
```

---

### Detailed Request-Response Cycle

1. **User Action**: User clicks a link or submits a form
2. **Request Sent**: Browser sends HTTP request to server
3. **Routing**: Server routes request to appropriate Controller
4. **Controller Processing**: Controller receives request, validates input
5. **Model Interaction**: Controller calls Model methods to get/modify data
6. **Data Processing**: Model performs database operations, applies business rules
7. **Data Return**: Model returns data to Controller
8. **View Selection**: Controller selects appropriate View
9. **View Rendering**: View receives data and generates HTML
10. **Response Sent**: HTML sent back to user's browser
11. **Display**: Browser renders the page for user

---

### Benefits of MVC Architecture

| Benefit                    | Description                                                              |
| -------------------------- | ------------------------------------------------------------------------ |
| **Separation of Concerns** | Each component has a single responsibility, making code cleaner          |
| **Maintainability**        | Changes in one component don't affect others                             |
| **Testability**            | Each component can be tested independently                               |
| **Parallel Development**   | Frontend and backend developers can work simultaneously                  |
| **Reusability**            | Models and Views can be reused across different parts of the application |
| **Scalability**            | Easier to scale individual components                                    |
| **Code Organization**      | Clear structure makes codebase easier to navigate                        |

---

### MVC in Different Frameworks

| Framework     | Language   | MVC Implementation           |
| ------------- | ---------- | ---------------------------- |
| Django        | Python     | MTV (Model-Template-View)    |
| Ruby on Rails | Ruby       | Traditional MVC              |
| ASP.NET MVC   | C#         | Traditional MVC              |
| Spring MVC    | Java       | Traditional MVC              |
| Laravel       | PHP        | Traditional MVC              |
| Express.js    | JavaScript | Flexible (can implement MVC) |

---

### Common Mistakes to Avoid in MVC

1. **Fat Controllers**: Putting too much logic in controllers instead of models
2. **Logic in Views**: Writing business logic in templates
3. **Direct Model-View Communication**: Views should not directly access Models
4. **Mixing Concerns**: Database queries in controllers or Views
5. **Ignoring Validation**: Not validating data in Models

---

### Simple MVC Example (Conceptual)

```python
# MODEL - handles data
class Article:
    def __init__(self, title, content):
        self.title = title
        self.content = content

    @staticmethod
    def get_all():
        # Returns all articles from database
        return [Article("Hello", "World")]

# VIEW - displays data
def render_articles(articles):
    html = "<ul>"
    for article in articles:
        html += f"<li>{article.title}</li>"
    html += "</ul>"
    return html

# CONTROLLER - handles request
def article_list(request):
    articles = Article.get_all()  # Get data from Model
    return render_articles(articles)  # Pass to View
```

---

## 3.2 Role of Backend in Web Applications

### What is Backend?

The **backend** (also called server-side) is the part of a web application that users don't see. It runs on the server and handles:

- Processing user requests
- Managing databases
- Running business logic
- Authenticating users
- Sending responses to the frontend

While the frontend is what users interact with (buttons, forms, colors), the backend is what makes everything actually work behind the scenes.

---

### Frontend vs Backend

| Aspect         | Frontend                  | Backend                          |
| -------------- | ------------------------- | -------------------------------- |
| **Location**   | User's browser            | Web server                       |
| **Languages**  | HTML, CSS, JavaScript     | Python, Java, PHP, Ruby, Node.js |
| **Visibility** | User can see and interact | Hidden from user                 |
| **Purpose**    | User interface            | Data processing, logic           |
| **Examples**   | Buttons, forms, layouts   | Database, APIs, authentication   |

---

### Core Responsibilities of Backend

#### 1. Server Logic

The server is a computer that:

- **Listens** for incoming requests from clients
- **Processes** those requests
- **Sends** appropriate responses back

**How servers work:**

1. Server runs continuously, listening on a port (e.g., port 80 or 443)
2. When a request arrives, server creates a new thread/process to handle it
3. Request is processed and response is generated
4. Response is sent back to the client
5. Connection may be kept open or closed

```
Client (Browser)          Server
      │                     │
      │── HTTP Request ────►│
      │   (GET /home)       │
      │                     │ Process request
      │                     │ Generate response
      │◄── HTTP Response ───│
      │   (HTML page)       │
```

---

#### 2. Routing

**Routing** is the process of determining what code should run based on the URL requested. It maps URLs to specific functions or handlers.

**Why routing matters:**

- `/home` → Show homepage
- `/products` → Show product list
- `/products/123` → Show product with ID 123
- `/login` → Show login form
- `/api/users` → Return user data as JSON

**Types of routes:**

- **Static routes**: Exact URL match (`/about`, `/contact`)
- **Dynamic routes**: URLs with variables (`/users/<id>`, `/products/<category>`)
- **Wildcard routes**: Catch-all patterns (`/blog/*`)

**Route parameters:**

- **Path parameters**: `/users/123` (123 is the user ID)
- **Query parameters**: `/search?q=python&page=2`

---

#### 3. Business Logic

**Business logic** is the code that implements the rules and operations specific to your application. It's the "brain" of your application.

**Examples of business logic:**

- Calculating total price with discounts
- Checking if user has permission to perform an action
- Validating that email addresses are unique
- Processing payment transactions
- Generating reports from data

**Business logic should:**

- Be separate from presentation (Views)
- Be testable in isolation
- Be reusable across different interfaces
- Handle edge cases and errors

**Example business rules:**

```
- Users must be 18+ to register
- Orders over $100 get free shipping
- Passwords must be at least 8 characters
- Users can only edit their own posts
- Maximum 5 items per cart
```

---

#### 4. Security Layers

Security is critical for backend applications. Multiple layers protect the application:

**a) Authentication (Who are you?)**

- Verifying user identity
- Login systems (username/password)
- Multi-factor authentication
- OAuth/Social login

**b) Authorization (What can you do?)**

- Checking user permissions
- Role-based access control (Admin, User, Guest)
- Resource ownership verification

**c) Input Validation**

- Sanitizing user input
- Preventing SQL injection
- Preventing XSS (Cross-Site Scripting)
- Validating data types and formats

**d) Data Protection**

- Encrypting sensitive data
- Hashing passwords (never store plain text!)
- HTTPS for data in transit
- Secure session management

**e) CSRF Protection**

- Preventing cross-site request forgery
- Using CSRF tokens in forms

**f) Rate Limiting**

- Preventing brute force attacks
- Limiting API requests per user

---

### The Backend Request Flow

```
┌──────────────────────────────────────────────────────────────┐
│                        BACKEND SERVER                         │
│                                                              │
│   ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐   │
│   │  LOAD   │    │ ROUTING │    │SECURITY │    │BUSINESS │   │
│   │BALANCER │───►│  LAYER  │───►│  LAYER  │───►│  LOGIC  │   │
│   └─────────┘    └─────────┘    └─────────┘    └────┬────┘   │
│                                                     │        │
│   ┌─────────┐    ┌─────────┐    ┌─────────┐        │        │
│   │RESPONSE │◄───│  VIEW   │◄───│ MODEL   │◄───────┘        │
│   │ SENT    │    │ RENDER  │    │(DATABASE)│                 │
│   └─────────┘    └─────────┘    └─────────┘                  │
└──────────────────────────────────────────────────────────────┘
```

---

### Backend Technologies

**Programming Languages:**
| Language | Popular Frameworks |
|----------|-------------------|
| Python | Django, Flask, FastAPI |
| JavaScript | Node.js, Express.js |
| Java | Spring Boot, Jakarta EE |
| C# | ASP.NET Core |
| PHP | Laravel, Symfony |
| Ruby | Ruby on Rails |
| Go | Gin, Echo |

**Databases:**
| Type | Examples | Best For |
|------|----------|----------|
| Relational (SQL) | PostgreSQL, MySQL, SQLite | Structured data, relationships |
| NoSQL Document | MongoDB, CouchDB | Flexible schemas, JSON data |
| NoSQL Key-Value | Redis, Memcached | Caching, sessions |

**Web Servers:**

- Nginx
- Apache
- Gunicorn (Python)
- uWSGI

---

### API Types

Backend often exposes **APIs** (Application Programming Interfaces) for frontend communication:

**REST API (Representational State Transfer):**

- Uses HTTP methods (GET, POST, PUT, DELETE)
- Stateless communication
- Returns JSON or XML
- Most common API style

**GraphQL:**

- Single endpoint
- Client specifies exact data needed
- Reduces over-fetching

**WebSocket:**

- Real-time bidirectional communication
- Good for chat apps, live updates

---

## 3.3 Backend Web Framework: Django

### What is Django?

**Django** is a high-level Python web framework that enables rapid development of secure and maintainable websites. It was created in 2003 by Adrian Holovaty and Simon Willison at the Lawrence Journal-World newspaper.

**Django's Philosophy:**

- **"Batteries included"**: Comes with everything you need out of the box
- **DRY (Don't Repeat Yourself)**: Encourages code reuse
- **Rapid Development**: Build applications quickly
- **Security First**: Protects against common vulnerabilities

---

### Why Choose Django?

| Feature                             | Benefit                                      |
| ----------------------------------- | -------------------------------------------- |
| **Built-in Admin Panel**            | Automatic admin interface for managing data  |
| **ORM (Object-Relational Mapping)** | Work with databases using Python, not SQL    |
| **Security**                        | Protection against SQL injection, XSS, CSRF  |
| **Scalability**                     | Powers large sites like Instagram, Pinterest |
| **Large Community**                 | Extensive documentation and packages         |
| **Authentication System**           | Built-in user management                     |

---

### MTV Pattern (Django's Version of MVC)

Django uses **MTV (Model-Template-View)** pattern, which is similar to MVC but with different naming:

| MVC        | Django MTV   | Role                                |
| ---------- | ------------ | ----------------------------------- |
| Model      | **Model**    | Data structure and database         |
| View       | **Template** | HTML presentation                   |
| Controller | **View**     | Business logic and request handling |

**Why the name difference?**

- In Django, a "View" is a Python function/class that handles requests (like a Controller)
- A "Template" is the HTML file that displays data (like a View in MVC)

---

### MTV Flow in Django

```
┌─────────────┐
│    USER     │
└──────┬──────┘
       │ 1. Request: /articles/
       ▼
┌─────────────┐
│  urls.py    │ ◄──── URL Router
└──────┬──────┘
       │ 2. Routes to view function
       ▼
┌─────────────┐
│  views.py   │ ◄──── VIEW (handles logic)
└──────┬──────┘
       │ 3. Queries database
       ▼
┌─────────────┐
│  models.py  │ ◄──── MODEL (data layer)
└──────┬──────┘
       │ 4. Returns data
       ▼
┌─────────────┐
│  views.py   │ ◄──── Passes data to template
└──────┬──────┘
       │ 5. Renders template
       ▼
┌─────────────┐
│template.html│ ◄──── TEMPLATE (presentation)
└──────┬──────┘
       │ 6. Returns HTML response
       ▼
┌─────────────┐
│    USER     │ ◄──── Sees the page
└─────────────┘
```

---

### Django Project Structure

When you create a Django project, the following structure is generated:

```
myproject/                 # Root directory (any name)
│
├── manage.py             # Command-line utility for Django
│
├── myproject/            # Project package (same name as root)
│   ├── __init__.py       # Makes this a Python package
│   ├── settings.py       # Project configuration
│   ├── urls.py           # Main URL routing
│   ├── asgi.py           # ASGI configuration (async)
│   └── wsgi.py           # WSGI configuration (deployment)
│
└── myapp/                # Your application (created separately)
    ├── __init__.py       # Makes this a Python package
    ├── admin.py          # Admin panel configuration
    ├── apps.py           # App configuration
    ├── models.py         # Database models
    ├── views.py          # View functions/classes
    ├── urls.py           # App-specific URL routing
    ├── tests.py          # Unit tests
    └── migrations/       # Database migration files
        └── __init__.py
```

---

### Key Files Explained

#### 1. manage.py

The command-line tool for interacting with Django:

```bash
python manage.py runserver     # Start development server
python manage.py makemigrations # Create migration files
python manage.py migrate       # Apply migrations to database
python manage.py createsuperuser # Create admin user
```

#### 2. settings.py

Contains all project configuration:

- Database settings
- Installed apps
- Middleware
- Templates configuration
- Static files settings
- Security settings

#### 3. urls.py

Maps URLs to views:

```python
from django.urls import path
from myapp import views

urlpatterns = [
    path('', views.home),
    path('about/', views.about),
]
```

#### 4. models.py

Defines database structure:

```python
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```

#### 5. views.py

Handles request processing:

```python
from django.shortcuts import render
from .models import Article

def article_list(request):
    articles = Article.objects.all()
    return render(request, 'articles.html', {'articles': articles})
```

---

### Django Apps

A Django **project** can contain multiple **apps**. An app is a web application that does something specific.

**Project vs App:**

- **Project**: The entire website (configuration, settings)
- **App**: A specific feature/module (blog, users, products)

**Example structure with multiple apps:**

```
myproject/
├── manage.py
├── myproject/          # Project settings
├── blog/               # Blog app
├── users/              # User management app
├── products/           # E-commerce app
└── api/                # REST API app
```

**Creating an app:**

```bash
python manage.py startapp blog
```

**Registering an app in settings.py:**

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    # ... other built-in apps
    'blog',    # Your custom app
    'users',   # Another custom app
]
```

---

### Simple Django Setup

```python
# 1. Install Django
# pip install django

# 2. Create project
# django-admin startproject mysite

# 3. Create app
# cd mysite
# python manage.py startapp blog

# 4. Register app in settings.py
# Add 'blog' to INSTALLED_APPS

# 5. Run server
# python manage.py runserver
```

---

## 3.4 Handling Requests and Responses

### What is HTTP?

**HTTP (HyperText Transfer Protocol)** is the foundation of data communication on the web. It defines how messages are formatted and transmitted between clients (browsers) and servers.

---

### HTTP Request Structure

When a browser sends a request, it includes:

```
┌─────────────────────────────────────────┐
│            HTTP REQUEST                 │
├─────────────────────────────────────────┤
│ Request Line:                           │
│   GET /articles/ HTTP/1.1               │
├─────────────────────────────────────────┤
│ Headers:                                │
│   Host: example.com                     │
│   User-Agent: Mozilla/5.0               │
│   Accept: text/html                     │
│   Cookie: session_id=abc123             │
├─────────────────────────────────────────┤
│ Body (optional):                        │
│   {"name": "John", "email": "..."}     │
└─────────────────────────────────────────┘
```

---

### HTTP Methods

| Method     | Purpose                 | Idempotent | Request Body |
| ---------- | ----------------------- | ---------- | ------------ |
| **GET**    | Retrieve data           | Yes        | No           |
| **POST**   | Create new resource     | No         | Yes          |
| **PUT**    | Update/replace resource | Yes        | Yes          |
| **PATCH**  | Partial update          | No         | Yes          |
| **DELETE** | Remove resource         | Yes        | No           |

**Idempotent**: Running the same request multiple times produces the same result.

---

### HTTP Response Structure

```
┌─────────────────────────────────────────┐
│            HTTP RESPONSE                │
├─────────────────────────────────────────┤
│ Status Line:                            │
│   HTTP/1.1 200 OK                       │
├─────────────────────────────────────────┤
│ Headers:                                │
│   Content-Type: text/html               │
│   Content-Length: 1234                  │
│   Set-Cookie: session_id=xyz789         │
├─────────────────────────────────────────┤
│ Body:                                   │
│   <html>                                │
│     <body>Hello, World!</body>          │
│   </html>                               │
└─────────────────────────────────────────┘
```

---

### HTTP Status Codes

| Code Range | Category      | Common Examples                                                 |
| ---------- | ------------- | --------------------------------------------------------------- |
| **1xx**    | Informational | 100 Continue                                                    |
| **2xx**    | Success       | 200 OK, 201 Created, 204 No Content                             |
| **3xx**    | Redirection   | 301 Moved, 302 Found, 304 Not Modified                          |
| **4xx**    | Client Error  | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found |
| **5xx**    | Server Error  | 500 Internal Error, 502 Bad Gateway, 503 Unavailable            |

---

### Django Views

A **view** in Django is a Python function or class that receives a web request and returns a web response. Views contain the logic that processes requests.

---

### Function-Based Views (FBV)

The simplest type of view in Django:

```python
from django.http import HttpResponse

def hello(request):
    return HttpResponse("Hello, World!")
```

**The request object contains:**

- `request.method` - HTTP method (GET, POST, etc.)
- `request.GET` - Query parameters dictionary
- `request.POST` - POST data dictionary
- `request.body` - Raw request body
- `request.headers` - HTTP headers
- `request.user` - Currently logged-in user
- `request.session` - Session data

---

### Handling Different HTTP Methods

```python
from django.http import HttpResponse

def article_view(request):
    if request.method == 'GET':
        return HttpResponse("Showing articles")

    elif request.method == 'POST':
        return HttpResponse("Creating article")

    elif request.method == 'DELETE':
        return HttpResponse("Deleting article")
```

---

### URL Configuration

Connect URLs to views in **urls.py**:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('articles/', views.article_list, name='articles'),
    path('articles/<int:id>/', views.article_detail, name='article_detail'),
]
```

**URL Parameters:**

- `<int:id>` - Captures an integer
- `<str:slug>` - Captures a string
- `<uuid:pk>` - Captures a UUID

---

### Accessing URL Parameters

```python
def article_detail(request, id):
    # id is automatically passed from the URL
    return HttpResponse(f"Article ID: {id}")
```

---

### Query Parameters

Access GET parameters from the URL:

```python
# URL: /search/?q=python&page=2

def search(request):
    query = request.GET.get('q', '')       # 'python'
    page = request.GET.get('page', '1')    # '2'
    return HttpResponse(f"Searching: {query}, Page: {page}")
```

---

### Rendering Templates

Returning HTML using templates:

```python
from django.shortcuts import render

def article_list(request):
    articles = [
        {'title': 'First Article', 'content': 'Hello'},
        {'title': 'Second Article', 'content': 'World'},
    ]
    return render(request, 'articles.html', {'articles': articles})
```

**Template (articles.html):**

```html
<ul>
  {% for article in articles %}
  <li>{{ article.title }}</li>
  {% endfor %}
</ul>
```

---

### JSON Responses

For APIs, return JSON instead of HTML:

```python
from django.http import JsonResponse

def api_articles(request):
    data = {
        'articles': [
            {'id': 1, 'title': 'First'},
            {'id': 2, 'title': 'Second'},
        ],
        'count': 2
    }
    return JsonResponse(data)
```

**Response:**

```json
{
  "articles": [
    { "id": 1, "title": "First" },
    { "id": 2, "title": "Second" }
  ],
  "count": 2
}
```

---

### Returning Lists as JSON

By default, `JsonResponse` expects a dictionary. For lists, use `safe=False`:

```python
def api_list(request):
    data = [1, 2, 3, 4, 5]
    return JsonResponse(data, safe=False)
```

---

### Different Response Types

```python
from django.http import (
    HttpResponse,
    JsonResponse,
    HttpResponseRedirect,
    HttpResponseNotFound,
)
from django.shortcuts import redirect

# Plain text
def text_response(request):
    return HttpResponse("Plain text", content_type="text/plain")

# HTML
def html_response(request):
    return HttpResponse("<h1>Hello</h1>")

# JSON
def json_response(request):
    return JsonResponse({'status': 'ok'})

# Redirect
def redirect_view(request):
    return redirect('/home/')  # or HttpResponseRedirect('/home/')

# 404 Not Found
def not_found(request):
    return HttpResponseNotFound("Page not found")
```

---

### Setting Custom Status Codes

```python
from django.http import HttpResponse, JsonResponse

def custom_status(request):
    return HttpResponse("Created!", status=201)

def json_error(request):
    return JsonResponse({'error': 'Bad request'}, status=400)
```

---

### Complete View Example

```python
from django.http import JsonResponse, HttpResponse
from django.shortcuts import render, get_object_or_404

def article_api(request, id=None):
    """Handle article CRUD operations"""

    if request.method == 'GET':
        if id:
            # Return single article
            return JsonResponse({'id': id, 'title': 'Sample'})
        else:
            # Return all articles
            return JsonResponse({'articles': []})

    elif request.method == 'POST':
        # Create new article
        return JsonResponse({'message': 'Created'}, status=201)

    elif request.method == 'DELETE':
        if id:
            # Delete article
            return JsonResponse({'message': 'Deleted'})
        return JsonResponse({'error': 'ID required'}, status=400)

    # Method not allowed
    return HttpResponse(status=405)
```

---

### Summary Table: Request and Response

| Concept           | Description                                     |
| ----------------- | ----------------------------------------------- |
| **HTTP Request**  | Message from client to server                   |
| **HTTP Response** | Message from server to client                   |
| **HTTP Methods**  | GET, POST, PUT, PATCH, DELETE                   |
| **Status Codes**  | 2xx success, 4xx client error, 5xx server error |
| **View**          | Python function/class handling requests         |
| **render()**      | Returns HTML from template                      |
| **JsonResponse**  | Returns JSON data                               |
| **redirect()**    | Sends user to different URL                     |

---

## 3.5 Form Data Handling and Sessions

### What are HTML Forms?

**HTML forms** are the primary way users submit data to a web server. Forms allow users to:

- Enter text (usernames, passwords, comments)
- Select options (dropdowns, checkboxes, radio buttons)
- Upload files
- Submit data for processing

---

### How Forms Work

```
┌─────────────────────────────────────────────────────────┐
│                    FORM WORKFLOW                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   1. User fills out form in browser                     │
│                    ↓                                     │
│   2. User clicks Submit button                          │
│                    ↓                                     │
│   3. Browser sends POST request with form data          │
│                    ↓                                     │
│   4. Server receives and processes data                 │
│                    ↓                                     │
│   5. Server validates data                              │
│                    ↓                                     │
│   6. Server saves data / performs action                │
│                    ↓                                     │
│   7. Server sends response (redirect or page)           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### HTTP Methods for Forms

| Method   | Usage                               | Data Location                    |
| -------- | ----------------------------------- | -------------------------------- |
| **GET**  | Retrieving data, search forms       | URL query string (`?name=value`) |
| **POST** | Submitting data, creating resources | Request body (hidden from URL)   |

**When to use each:**

- **GET**: Search forms, filters, bookmarkable pages
- **POST**: Login forms, registration, file uploads, sensitive data

---

### Basic HTML Form

```html
<form method="POST" action="/submit/">
  <label for="username">Username:</label>
  <input type="text" id="username" name="username" />

  <label for="email">Email:</label>
  <input type="email" id="email" name="email" />

  <button type="submit">Submit</button>
</form>
```

**Key attributes:**

- `method`: HTTP method (GET or POST)
- `action`: URL where form data is sent
- `name`: Field name used to access data on server

---

### Processing Form Data in Django

#### Accessing POST Data

```python
from django.http import HttpResponse
from django.shortcuts import render

def register(request):
    if request.method == 'POST':
        # Access form data using field names
        username = request.POST.get('username')
        email = request.POST.get('email')

        # Process the data (save to database, etc.)
        return HttpResponse(f"Welcome, {username}!")

    # Show empty form for GET request
    return render(request, 'register.html')
```

---

#### Accessing GET Data

```python
def search(request):
    # Access query parameters
    query = request.GET.get('q', '')  # Default to empty string
    page = request.GET.get('page', '1')

    # Perform search with query
    return HttpResponse(f"Searching for: {query}")
```

---

### Form Data Methods

| Method                                 | Description                               |
| -------------------------------------- | ----------------------------------------- |
| `request.POST.get('field')`            | Get single value, returns None if missing |
| `request.POST.get('field', 'default')` | Get value with default                    |
| `request.POST['field']`                | Get value, raises error if missing        |
| `request.POST.getlist('field')`        | Get multiple values (checkboxes)          |

---

### What is CSRF?

**CSRF (Cross-Site Request Forgery)** is a security attack where a malicious website tricks a user's browser into making unwanted requests to another site where the user is authenticated.

**Example attack scenario:**

1. User logs into their bank website
2. User visits a malicious website (in another tab)
3. Malicious site contains hidden form that submits to bank
4. Bank thinks the request is legitimate (user is logged in)
5. Money is transferred without user's knowledge

---

### How CSRF Protection Works

Django generates a unique **CSRF token** for each user session. This token must be included in every POST form. When the form is submitted, Django verifies the token matches.

```
┌─────────────────────────────────────────────────────────┐
│                  CSRF PROTECTION FLOW                    │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   1. User requests a page with a form                   │
│                    ↓                                     │
│   2. Django generates unique CSRF token                 │
│                    ↓                                     │
│   3. Token is embedded in the form (hidden field)       │
│                    ↓                                     │
│   4. User submits form with token                       │
│                    ↓                                     │
│   5. Django validates token matches session             │
│                    ↓                                     │
│   6. If valid → Process request                         │
│      If invalid → Reject request (403 Forbidden)        │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### Using CSRF Token in Django

**In templates, add the token inside your form:**

```html
<form method="POST" action="/submit/">
  {% csrf_token %}

  <input type="text" name="username" />
  <button type="submit">Submit</button>
</form>
```

The `{% csrf_token %}` tag generates a hidden input field:

```html
<input type="hidden" name="csrfmiddlewaretoken" value="abc123xyz..." />
```

---

### CSRF for AJAX Requests

For JavaScript/AJAX requests, include the token in headers:

```javascript
// Get token from cookie
function getCookie(name) {
  let cookieValue = null;
  if (document.cookie && document.cookie !== "") {
    const cookies = document.cookie.split(";");
    for (let i = 0; i < cookies.length; i++) {
      const cookie = cookies[i].trim();
      if (cookie.substring(0, name.length + 1) === name + "=") {
        cookieValue = cookie.substring(name.length + 1);
        break;
      }
    }
  }
  return cookieValue;
}

// Include in fetch request
fetch("/api/submit/", {
  method: "POST",
  headers: {
    "X-CSRFToken": getCookie("csrftoken"),
    "Content-Type": "application/json",
  },
  body: JSON.stringify(data),
});
```

---

### What are Sessions?

**Sessions** allow the server to remember information about a user across multiple requests. Since HTTP is stateless (each request is independent), sessions provide a way to maintain state.

**Common session uses:**

- Keep users logged in
- Store shopping cart contents
- Remember user preferences
- Track form progress (multi-step wizards)

---

### How Sessions Work

```
┌─────────────────────────────────────────────────────────┐
│                   SESSION WORKFLOW                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   1. User makes first request to server                 │
│                    ↓                                     │
│   2. Server creates unique Session ID                   │
│                    ↓                                     │
│   3. Session ID sent to browser in cookie               │
│                    ↓                                     │
│   4. Browser stores cookie                              │
│                    ↓                                     │
│   5. Every future request includes Session ID cookie    │
│                    ↓                                     │
│   6. Server uses Session ID to retrieve user's data     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### Session Storage Options

| Storage Type | Description                       | Pros                 | Cons                  |
| ------------ | --------------------------------- | -------------------- | --------------------- |
| **Database** | Sessions stored in database       | Persistent, scalable | Slower queries        |
| **Cache**    | Sessions stored in memory (Redis) | Very fast            | Lost on restart       |
| **File**     | Sessions stored as files          | Simple               | Not scalable          |
| **Cookie**   | Data stored in cookie itself      | No server storage    | Size limits, security |

---

### Using Sessions in Django

Django provides built-in session support. Sessions work like a dictionary:

```python
def login_view(request):
    if request.method == 'POST':
        username = request.POST.get('username')

        # Store data in session
        request.session['username'] = username
        request.session['is_logged_in'] = True

        return redirect('/dashboard/')

    return render(request, 'login.html')


def dashboard(request):
    # Retrieve data from session
    username = request.session.get('username', 'Guest')
    is_logged_in = request.session.get('is_logged_in', False)

    if not is_logged_in:
        return redirect('/login/')

    return HttpResponse(f"Welcome, {username}!")


def logout_view(request):
    # Clear session data
    request.session.flush()  # Removes all session data
    return redirect('/login/')
```

---

### Session Methods

| Method                                | Description            |
| ------------------------------------- | ---------------------- |
| `request.session['key'] = value`      | Set session value      |
| `request.session.get('key')`          | Get session value      |
| `request.session.get('key', default)` | Get with default       |
| `del request.session['key']`          | Delete specific key    |
| `request.session.flush()`             | Clear all session data |
| `request.session.set_expiry(seconds)` | Set expiration time    |

---

### Sessions vs Cookies

| Aspect          | Cookies               | Sessions         |
| --------------- | --------------------- | ---------------- |
| **Storage**     | Browser (client)      | Server           |
| **Size Limit**  | ~4KB                  | No limit         |
| **Security**    | Visible to user       | Hidden from user |
| **Data Access** | JavaScript can access | Server only      |
| **Use Case**    | Preferences, tokens   | User data, cart  |

---

### Django Forms (Forms API)

Django provides a forms framework for easier form handling:

```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)

def contact(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # Access cleaned data
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']
            # Process form...
            return HttpResponse("Thank you!")
    else:
        form = ContactForm()

    return render(request, 'contact.html', {'form': form})
```

**Template:**

```html
<form method="POST">
  {% csrf_token %} {{ form.as_p }}
  <button type="submit">Send</button>
</form>
```

---

### Summary: Forms and Sessions

| Concept          | Purpose                        |
| ---------------- | ------------------------------ |
| **HTML Forms**   | Collect user input             |
| **POST Method**  | Submit data securely           |
| **GET Method**   | Retrieve data, search          |
| **CSRF Token**   | Prevent cross-site attacks     |
| **Sessions**     | Maintain state across requests |
| **Django Forms** | Simplified form handling       |

---

## 3.6 Routing, Middleware, and Templating Concepts

### What is Routing?

**Routing** is the mechanism that maps URLs to specific code (views/controllers). When a user visits a URL, the routing system determines which function should handle the request.

---

### URL Dispatcher in Django

Django uses a **URL dispatcher** defined in `urls.py` files. It matches incoming URLs against patterns and calls the corresponding view.

**How URL matching works:**

1. Request comes in with a URL (e.g., `/articles/5/`)
2. Django checks patterns in order from top to bottom
3. First matching pattern wins
4. Associated view function is called
5. View returns a response

---

### Basic URL Configuration

**Project-level urls.py:**

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),  # Include app URLs
    path('api/', include('api.urls')),
]
```

**App-level urls.py (blog/urls.py):**

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.post_list, name='post_list'),
    path('<int:pk>/', views.post_detail, name='post_detail'),
    path('create/', views.post_create, name='post_create'),
]
```

---

### URL Patterns

| Pattern          | Matches                 | Example               |
| ---------------- | ----------------------- | --------------------- |
| `''`             | Empty path              | `/blog/`              |
| `'about/'`       | Exact string            | `/about/`             |
| `'<int:id>/'`    | Integer                 | `/blog/123/`          |
| `'<str:slug>/'`  | String (no slashes)     | `/blog/my-post/`      |
| `'<slug:slug>/'` | Slug format             | `/blog/my-post-123/`  |
| `'<uuid:id>/'`   | UUID                    | `/user/550e8400-...`  |
| `'<path:path>/'` | Any path (with slashes) | `/files/dir/file.txt` |

---

### Path Converters

```python
from django.urls import path

urlpatterns = [
    # Integer parameter
    path('article/<int:id>/', views.article_detail),

    # String parameter
    path('category/<str:name>/', views.category_view),

    # Slug parameter (letters, numbers, hyphens, underscores)
    path('post/<slug:slug>/', views.post_detail),

    # Multiple parameters
    path('archive/<int:year>/<int:month>/', views.archive),
]
```

**In views:**

```python
def article_detail(request, id):
    # id is automatically converted to integer
    return HttpResponse(f"Article {id}")

def archive(request, year, month):
    return HttpResponse(f"Archive: {year}/{month}")
```

---

### Named URLs

Give URLs names for easy referencing:

```python
path('articles/', views.article_list, name='article_list'),
path('articles/<int:id>/', views.article_detail, name='article_detail'),
```

**Using named URLs in templates:**

```html
<a href="{% url 'article_list' %}">All Articles</a>
<a href="{% url 'article_detail' id=5 %}">Article 5</a>
```

**Using named URLs in views:**

```python
from django.urls import reverse
from django.shortcuts import redirect

def my_view(request):
    url = reverse('article_detail', args=[5])  # '/articles/5/'
    return redirect('article_list')  # Redirect by name
```

---

### What is Middleware?

**Middleware** is software that sits between the request and response, processing each one. It's like a series of filters that every request/response passes through.

```
┌─────────────────────────────────────────────────────────┐
│                   MIDDLEWARE FLOW                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   REQUEST → [MW1] → [MW2] → [MW3] → VIEW                │
│                                                         │
│   RESPONSE ← [MW1] ← [MW2] ← [MW3] ← VIEW               │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### Middleware Use Cases

| Use Case               | Description                    |
| ---------------------- | ------------------------------ |
| **Authentication**     | Check if user is logged in     |
| **Logging**            | Log all requests for debugging |
| **Security**           | Add security headers           |
| **CORS**               | Handle cross-origin requests   |
| **Compression**        | Compress responses             |
| **Caching**            | Cache responses                |
| **Session Management** | Handle user sessions           |

---

### Django Built-in Middleware

Django comes with several middleware enabled by default in `settings.py`:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',      # Security headers
    'django.contrib.sessions.middleware.SessionMiddleware',  # Sessions
    'django.middleware.common.CommonMiddleware',          # Common utilities
    'django.middleware.csrf.CsrfViewMiddleware',          # CSRF protection
    'django.contrib.auth.middleware.AuthenticationMiddleware',  # Auth
    'django.contrib.messages.middleware.MessageMiddleware',  # Messages
    'django.middleware.clickjacking.XFrameOptionsMiddleware',  # Clickjacking
]
```

---

### Creating Custom Middleware

**Function-based middleware:**

```python
def simple_middleware(get_response):
    # One-time configuration (when server starts)

    def middleware(request):
        # Code executed BEFORE the view
        print(f"Request to: {request.path}")

        response = get_response(request)  # Call the view

        # Code executed AFTER the view
        print(f"Response status: {response.status_code}")

        return response

    return middleware
```

**Class-based middleware:**

```python
class SimpleMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        # One-time configuration

    def __call__(self, request):
        # Before view
        print(f"Request: {request.method} {request.path}")

        response = self.get_response(request)

        # After view
        return response
```

---

### Registering Custom Middleware

Add to `settings.py`:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    # ... other middleware
    'myapp.middleware.SimpleMiddleware',  # Your custom middleware
]
```

**Order matters!** Middleware is executed top to bottom for requests, and bottom to top for responses.

---

### Middleware Example: Request Timer

```python
import time

class RequestTimerMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        start_time = time.time()

        response = self.get_response(request)

        duration = time.time() - start_time
        print(f"{request.path} took {duration:.2f} seconds")

        return response
```

---

### What is Templating?

**Templating** is the process of generating dynamic HTML by combining a template (HTML structure) with data. Templates separate presentation from logic.

---

### Django Template Language (DTL)

Django's template language allows you to:

- Insert dynamic data with `{{ variable }}`
- Use logic with `{% tag %}`
- Apply filters with `{{ variable|filter }}`

---

### Template Variables

Output data using double curly braces:

```html
<h1>{{ title }}</h1>
<p>Welcome, {{ user.username }}!</p>
<p>You have {{ message_count }} messages.</p>
```

**Accessing nested data:**

```html
{{ user.profile.avatar }} {{ article.author.name }} {{ items.0 }}
<!-- First item in list -->
```

---

### Template Tags

Control logic using `{% tag %}`:

**If/Else:**

```html
{% if user.is_authenticated %}
<p>Welcome back, {{ user.username }}!</p>
{% else %}
<p>Please <a href="/login/">log in</a>.</p>
{% endif %}
```

**For Loop:**

```html
<ul>
  {% for article in articles %}
  <li>{{ article.title }}</li>
  {% empty %}
  <li>No articles found.</li>
  {% endfor %}
</ul>
```

**Loop variables:**

```html
{% for item in items %} {{ forloop.counter }}
<!-- 1, 2, 3, ... -->
{{ forloop.counter0 }}
<!-- 0, 1, 2, ... -->
{{ forloop.first }}
<!-- True if first iteration -->
{{ forloop.last }}
<!-- True if last iteration -->
{% endfor %}
```

---

### Template Filters

Transform data using `|filter`:

```html
{{ name|upper }}
<!-- JOHN -->
{{ name|lower }}
<!-- john -->
{{ name|title }}
<!-- John Doe -->
{{ text|truncatewords:20 }}
<!-- First 20 words... -->
{{ date|date:"Y-m-d" }}
<!-- 2026-01-17 -->
{{ price|floatformat:2 }}
<!-- 19.99 -->
{{ list|length }}
<!-- 5 -->
{{ value|default:"N/A" }}
<!-- Shows "N/A" if empty -->
{{ html_content|safe }}
<!-- Renders HTML (careful!) -->
```

---

### Template Inheritance

Create a base template and extend it:

**base.html:**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>{% block title %}My Site{% endblock %}</title>
  </head>
  <body>
    <nav>
      <a href="/">Home</a>
      <a href="/about/">About</a>
    </nav>

    <main>{% block content %}{% endblock %}</main>

    <footer>&copy; 2026 My Site</footer>
  </body>
</html>
```

**home.html:**

```html
{% extends "base.html" %} {% block title %}Home - My Site{% endblock %} {% block
content %}
<h1>Welcome to My Site</h1>
<p>This is the homepage.</p>
{% endblock %}
```

---

### Including Templates

Reuse template fragments:

**navbar.html:**

```html
<nav>
  <a href="/">Home</a>
  <a href="/about/">About</a>
</nav>
```

**page.html:**

```html
{% include "navbar.html" %}

<h1>Page Content</h1>
```

**Passing variables to includes:**

```html
{% include "card.html" with title="My Card" content="Card content" %}
```

---

### Template Configuration

Configure templates in `settings.py`:

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # Project-wide templates
        'APP_DIRS': True,  # Look for templates in app directories
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

---

### Template Directory Structure

```
myproject/
├── templates/              # Project-wide templates
│   ├── base.html
│   └── navbar.html
│
├── blog/
│   └── templates/
│       └── blog/           # App-specific templates
│           ├── post_list.html
│           └── post_detail.html
│
└── users/
    └── templates/
        └── users/
            ├── login.html
            └── profile.html
```

---

### Summary: Routing, Middleware, and Templating

| Concept                  | Purpose                             |
| ------------------------ | ----------------------------------- | ---------- |
| **URL Dispatcher**       | Map URLs to views                   |
| **Path Converters**      | Extract parameters from URLs        |
| **Named URLs**           | Reference URLs by name              |
| **Middleware**           | Process requests/responses          |
| **Templates**            | Generate dynamic HTML               |
| **Template Tags**        | Add logic (`{% if %}`, `{% for %}`) |
| **Template Filters**     | Transform data (`{{ x               | upper }}`) |
| **Template Inheritance** | Reuse layouts (`{% extends %}`)     |

---

## 3.7 Overview and Comparison of Backend Frameworks

### What is a Web Framework?

A **web framework** is a software framework designed to support web application development. It provides:

- Pre-built components for common tasks
- Structure and conventions for organizing code
- Tools for handling HTTP requests/responses
- Database integration
- Security features
- Development utilities

Using a framework saves time, enforces best practices, and reduces the chance of security vulnerabilities.

---

### Types of Web Frameworks

| Type           | Description                                 | Examples                   |
| -------------- | ------------------------------------------- | -------------------------- |
| **Full-Stack** | Everything included (ORM, templates, admin) | Django, Rails, Laravel     |
| **Micro**      | Minimal core, add what you need             | Flask, Express.js, Sinatra |
| **API-First**  | Optimized for building APIs                 | FastAPI, .NET Web API      |

---

### Django (Python)

**Type:** Full-stack framework

**Philosophy:** "Batteries included" — comes with everything you need

**Key Features:**

- Built-in admin panel
- ORM (Object-Relational Mapping)
- Authentication system
- Form handling
- Template engine
- Security features (CSRF, XSS protection)
- URL routing
- Middleware support

**Best For:**

- Content management systems
- E-commerce sites
- Social networks
- Data-driven applications

**Pros:**
| Advantage | Description |
|-----------|-------------|
| Complete package | Everything included out of the box |
| Admin panel | Automatic admin interface |
| Security | Built-in protection against common attacks |
| Scalability | Powers Instagram, Pinterest |
| Documentation | Excellent official documentation |
| Community | Large, active community |

**Cons:**
| Disadvantage | Description |
|--------------|-------------|
| Monolithic | Can be overkill for small projects |
| Learning curve | Many concepts to learn |
| Less flexible | Encourages "Django way" |

**Sample Code:**

```python
# views.py
from django.shortcuts import render

def home(request):
    return render(request, 'home.html', {'title': 'Welcome'})
```

---

### Flask (Python)

**Type:** Micro framework

**Philosophy:** "Micro" — minimal core, maximum flexibility

**Key Features:**

- Lightweight and simple
- Jinja2 templating
- Werkzeug WSGI toolkit
- No ORM (use SQLAlchemy or others)
- No form validation (use WTForms)
- Extensible with plugins

**Best For:**

- Small to medium applications
- APIs and microservices
- Prototypes and MVPs
- Learning web development

**Pros:**
| Advantage | Description |
|-----------|-------------|
| Simple | Easy to learn and use |
| Flexible | Choose your own components |
| Lightweight | Small footprint |
| Explicit | No "magic" — clear code |

**Cons:**
| Disadvantage | Description |
|--------------|-------------|
| Manual setup | Must add extensions for features |
| No standard | Different projects structured differently |
| Less built-in | No admin, forms, or ORM |

**Sample Code:**

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('home.html', title='Welcome')
```

---

### FastAPI (Python)

**Type:** API-first micro framework

**Philosophy:** Fast, modern, automatic documentation

**Key Features:**

- Async support (asyncio)
- Automatic API documentation (Swagger/OpenAPI)
- Type hints for validation
- High performance
- Dependency injection
- OAuth2 support

**Best For:**

- REST APIs
- Microservices
- High-performance applications
- Machine learning APIs

**Pros:**
| Advantage | Description |
|-----------|-------------|
| Speed | One of the fastest Python frameworks |
| Modern | Async/await, type hints |
| Auto docs | Swagger UI generated automatically |
| Validation | Automatic request validation |

**Cons:**
| Disadvantage | Description |
|--------------|-------------|
| API-focused | Not ideal for traditional web apps |
| Newer | Smaller community than Django/Flask |
| Async complexity | Async can be harder to debug |

**Sample Code:**

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def home():
    return {"message": "Welcome"}

@app.get("/items/{item_id}")
async def get_item(item_id: int):
    return {"item_id": item_id}
```

---

### ASP.NET Core MVC (C#/.NET)

**Type:** Full-stack framework

**Philosophy:** Enterprise-grade, cross-platform

**Key Features:**

- Built on .NET Core (cross-platform)
- MVC architecture
- Entity Framework ORM
- Razor templating
- Dependency injection
- Strong typing
- Identity for authentication

**Best For:**

- Enterprise applications
- Windows environments
- Large-scale systems
- Microsoft ecosystem projects

**Pros:**
| Advantage | Description |
|-----------|-------------|
| Performance | Very fast, compiled language |
| Enterprise | Designed for large organizations |
| Tooling | Excellent Visual Studio integration |
| Type safety | Catches errors at compile time |

**Cons:**
| Disadvantage | Description |
|--------------|-------------|
| Complexity | Steeper learning curve |
| Verbose | More code than dynamic languages |
| Licensing | Some tools require licenses |

**Sample Code:**

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        ViewBag.Title = "Welcome";
        return View();
    }
}
```

---

### Ruby on Rails (Ruby)

**Type:** Full-stack framework

**Philosophy:** "Convention over configuration"

**Key Features:**

- MVC architecture
- Active Record ORM
- Scaffold generation
- Migrations for database
- Asset pipeline
- Action Cable (WebSockets)
- Strong conventions

**Best For:**

- Startups and MVPs
- Rapid prototyping
- E-commerce platforms
- Content-heavy sites

**Pros:**
| Advantage | Description |
|-----------|-------------|
| Rapid development | Build features quickly |
| Conventions | Less configuration needed |
| Elegant | Clean, readable Ruby code |
| Mature | 20+ years of development |

**Cons:**
| Disadvantage | Description |
|--------------|-------------|
| Performance | Slower than compiled languages |
| Hosting | Fewer hosting options |
| Ruby learning | Must learn Ruby language |

**Sample Code:**

```ruby
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end
end
```

---

### Spring Boot (Java)

**Type:** Full-stack framework

**Philosophy:** Enterprise Java made easier

**Key Features:**

- Standalone applications
- Embedded servers
- Auto-configuration
- Spring ecosystem integration
- Dependency injection
- Spring Security
- Spring Data JPA

**Best For:**

- Enterprise applications
- Microservices
- Large-scale systems
- Java ecosystem projects

**Pros:**
| Advantage | Description |
|-----------|-------------|
| Ecosystem | Huge Spring ecosystem |
| Enterprise | Industry standard for large systems |
| Performance | JVM optimization |
| Community | Massive community support |

**Cons:**
| Disadvantage | Description |
|--------------|-------------|
| Verbose | Lots of boilerplate code |
| Complexity | Many concepts to learn |
| Memory | JVM memory overhead |

**Sample Code:**

```java
@RestController
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "Welcome";
    }

    @GetMapping("/api/items/{id}")
    public Item getItem(@PathVariable Long id) {
        return itemService.findById(id);
    }
}
```

---

### Node.js with Express.js (JavaScript)

**Type:** Micro framework

**Philosophy:** Minimalist, unopinionated

**Key Features:**

- JavaScript on server
- Non-blocking I/O
- Huge npm ecosystem
- Middleware-based
- Template engine support
- WebSocket support

**Best For:**

- Real-time applications
- APIs and microservices
- Single-page apps (with React/Vue)
- JavaScript full-stack development

**Pros:**
| Advantage | Description |
|-----------|-------------|
| Same language | JavaScript frontend and backend |
| npm ecosystem | Millions of packages |
| Real-time | Excellent for sockets/streaming |
| Fast | V8 engine, non-blocking |

**Cons:**
| Disadvantage | Description |
|--------------|-------------|
| Callback hell | Async code can get messy |
| No structure | Must organize yourself |
| Single-threaded | CPU-intensive tasks block |

**Sample Code:**

```javascript
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Welcome");
});

app.get("/api/items/:id", (req, res) => {
  res.json({ id: req.params.id });
});

app.listen(3000);
```

---

### Framework Comparison Table

| Feature         | Django    | Flask     | FastAPI   | ASP.NET        | Rails  | Spring Boot | Express    |
| --------------- | --------- | --------- | --------- | -------------- | ------ | ----------- | ---------- |
| **Language**    | Python    | Python    | Python    | C#             | Ruby   | Java        | JavaScript |
| **Type**        | Full      | Micro     | API       | Full           | Full   | Full        | Micro      |
| **ORM**         | Built-in  | Extension | Extension | EF Core        | AR     | JPA         | Extension  |
| **Admin**       | Built-in  | No        | No        | No             | No     | No          | No         |
| **Learning**    | Medium    | Easy      | Easy      | Hard           | Medium | Hard        | Easy       |
| **Performance** | Good      | Good      | Excellent | Excellent      | Fair   | Excellent   | Good       |
| **Async**       | Limited   | Limited   | Native    | Native         | No     | Yes         | Native     |
| **Used By**     | Instagram | Netflix   | Microsoft | Stack Overflow | GitHub | Alibaba     | Netflix    |

---

### Choosing the Right Framework

**Choose Django if:**

- You want everything included
- You need an admin panel
- You're building a content-heavy site
- You prefer Python

**Choose Flask if:**

- You want full control
- You're building a small app or API
- You want to learn web fundamentals
- You prefer minimalism

**Choose FastAPI if:**

- You're building a REST API
- You need high performance
- You want automatic documentation
- You like type hints

**Choose ASP.NET if:**

- You're in a Microsoft environment
- You need enterprise features
- You prefer C# and strong typing
- You need high performance

**Choose Ruby on Rails if:**

- You want rapid development
- You prefer convention over configuration
- You're building an MVP
- You like Ruby

**Choose Spring Boot if:**

- You're building enterprise Java apps
- You need the Spring ecosystem
- You're working with microservices
- Your team knows Java

**Choose Express.js if:**

- You want JavaScript everywhere
- You're building real-time apps
- You're working with React/Vue
- You want flexibility

---

### Summary: Framework Comparison

| Consideration         | Recommendation                       |
| --------------------- | ------------------------------------ |
| **Beginner**          | Flask, Express                       |
| **Rapid Development** | Django, Rails                        |
| **APIs**              | FastAPI, Express                     |
| **Enterprise**        | Spring Boot, ASP.NET                 |
| **Full-Stack**        | Django, Rails, ASP.NET               |
| **Microservices**     | FastAPI, Express, Spring Boot        |
| **Real-Time**         | Express (Socket.io), Django Channels |

---

## 3.8 Database Integration: Relational vs NoSQL, CRUD Operations, ORM Concept

### What is a Database?

A **database** is an organized collection of structured data stored electronically. Databases allow applications to:

- Store data permanently
- Retrieve data efficiently
- Update and delete data
- Maintain data integrity
- Handle concurrent access

---

### Types of Databases

#### 1. Relational Databases (SQL)

**Relational databases** store data in tables with rows and columns. Tables can be related to each other through keys.

**Characteristics:**

- Data stored in structured tables
- Predefined schema (structure)
- Uses SQL (Structured Query Language)
- ACID compliant (Atomicity, Consistency, Isolation, Durability)
- Strong data integrity
- Relationships between tables

**Popular Relational Databases:**
| Database | Description |
|----------|-------------|
| **PostgreSQL** | Advanced, open-source, feature-rich |
| **MySQL** | Popular, fast, widely used |
| **SQLite** | Lightweight, file-based, embedded |
| **Oracle** | Enterprise, commercial |
| **SQL Server** | Microsoft, enterprise |

**Example Table Structure:**

```
┌─────────────────────────────────────────────────────┐
│                    users TABLE                       │
├─────┬──────────┬─────────────────────┬──────────────┤
│ id  │ username │ email               │ created_at   │
├─────┼──────────┼─────────────────────┼──────────────┤
│ 1   │ john     │ john@example.com    │ 2026-01-15   │
│ 2   │ jane     │ jane@example.com    │ 2026-01-16   │
│ 3   │ bob      │ bob@example.com     │ 2026-01-17   │
└─────┴──────────┴─────────────────────┴──────────────┘
```

---

#### 2. NoSQL Databases

**NoSQL databases** store data in flexible formats other than traditional tables. They are designed for scalability and handling unstructured data.

**Types of NoSQL Databases:**

| Type              | Description                   | Examples         |
| ----------------- | ----------------------------- | ---------------- |
| **Document**      | Stores JSON-like documents    | MongoDB, CouchDB |
| **Key-Value**     | Simple key-value pairs        | Redis, DynamoDB  |
| **Column-Family** | Columns grouped into families | Cassandra, HBase |
| **Graph**         | Nodes and relationships       | Neo4j, ArangoDB  |

**Characteristics:**

- Flexible schema (schema-less)
- Horizontal scaling
- High performance for specific use cases
- Good for unstructured/semi-structured data
- Eventually consistent (often)

**Example Document (MongoDB):**

```json
{
  "_id": "507f1f77bcf86cd799439011",
  "username": "john",
  "email": "john@example.com",
  "profile": {
    "bio": "Software developer",
    "avatar": "avatar.jpg"
  },
  "tags": ["developer", "python", "django"]
}
```

---

### Relational vs NoSQL Comparison

| Aspect             | Relational (SQL)               | NoSQL                       |
| ------------------ | ------------------------------ | --------------------------- |
| **Schema**         | Fixed, predefined              | Flexible, dynamic           |
| **Scaling**        | Vertical (bigger server)       | Horizontal (more servers)   |
| **Relationships**  | Strong (foreign keys)          | Weak or embedded            |
| **Query Language** | SQL                            | Varies (MongoDB uses JSON)  |
| **Transactions**   | ACID compliant                 | Often eventually consistent |
| **Best For**       | Structured data, relationships | Flexibility, large scale    |
| **Examples**       | Banking, ERP, CRM              | Social media, IoT, logs     |

---

### When to Use Which?

**Choose Relational Database When:**

- Data has clear relationships
- Data integrity is critical
- You need complex queries and joins
- Transactions are important
- Schema is well-defined

**Choose NoSQL When:**

- Schema is evolving/unknown
- You need horizontal scaling
- Handling large volumes of data
- Data is semi-structured or unstructured
- Speed is more important than consistency

---

### What is CRUD?

**CRUD** represents the four basic operations for persistent storage:

| Operation  | Description          | SQL Command | HTTP Method |
| ---------- | -------------------- | ----------- | ----------- |
| **C**reate | Add new data         | INSERT      | POST        |
| **R**ead   | Retrieve data        | SELECT      | GET         |
| **U**pdate | Modify existing data | UPDATE      | PUT/PATCH   |
| **D**elete | Remove data          | DELETE      | DELETE      |

---

### CRUD with Raw SQL

```sql
-- CREATE: Insert a new record
INSERT INTO users (username, email) VALUES ('john', 'john@example.com');

-- READ: Select records
SELECT * FROM users;                    -- All users
SELECT * FROM users WHERE id = 1;       -- Specific user
SELECT username, email FROM users;      -- Specific columns

-- UPDATE: Modify a record
UPDATE users SET email = 'newemail@example.com' WHERE id = 1;

-- DELETE: Remove a record
DELETE FROM users WHERE id = 1;
```

---

### What is ORM?

**ORM (Object-Relational Mapping)** is a technique that lets you work with databases using your programming language's objects instead of writing SQL queries.

**Benefits of ORM:**

- Write Python (or other language) instead of SQL
- Database-agnostic code
- Automatic SQL injection protection
- Easier to maintain and read
- Object-oriented database access

**How ORM Works:**

```
┌─────────────────────────────────────────────────────────┐
│                    ORM MAPPING                           │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   Python Class  ←─────────────→  Database Table         │
│   Class Attribute  ←───────────→  Table Column          │
│   Class Instance  ←────────────→  Table Row             │
│                                                         │
│   Example:                                              │
│   class User:        ←─────────→  users table           │
│       username       ←─────────→  username column       │
│       email          ←─────────→  email column          │
│                                                         │
│   user = User(...)   ←─────────→  One row in table      │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### Django ORM

Django includes a powerful built-in ORM. You define models as Python classes, and Django handles the database operations.

---

### Defining Models

Models are defined in `models.py`:

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    bio = models.TextField(blank=True)

    def __str__(self):
        return self.name


class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    published = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
```

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

---

### CRUD Operations with Django ORM

#### CREATE: Adding Records

```python
# Method 1: Create and save
author = Author(name='John Doe', email='john@example.com')
author.save()

# Method 2: Create in one step
author = Author.objects.create(
    name='Jane Doe',
    email='jane@example.com'
)

# Create with relationship
article = Article.objects.create(
    title='My First Article',
    content='This is the content.',
    author=author
)
```

---

#### READ: Retrieving Records

```python
# Get all records
all_articles = Article.objects.all()

# Get single record by primary key
article = Article.objects.get(pk=1)

# Get single record by field
author = Author.objects.get(email='john@example.com')

# Filter records
published = Article.objects.filter(published=True)
johns_articles = Article.objects.filter(author__name='John Doe')

# Exclude records
drafts = Article.objects.exclude(published=True)

# Order records
latest = Article.objects.order_by('-created_at')  # Descending
oldest = Article.objects.order_by('created_at')   # Ascending

# Limit results
first_five = Article.objects.all()[:5]

# Count records
count = Article.objects.count()

# Check existence
exists = Article.objects.filter(title='Hello').exists()
```

---

#### UPDATE: Modifying Records

```python
# Update single record
article = Article.objects.get(pk=1)
article.title = 'Updated Title'
article.save()

# Update multiple records
Article.objects.filter(published=False).update(published=True)
```

---

#### DELETE: Removing Records

```python
# Delete single record
article = Article.objects.get(pk=1)
article.delete()

# Delete multiple records
Article.objects.filter(published=False).delete()

# Delete all records
Article.objects.all().delete()
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

### CRUD in Views

```python
from django.shortcuts import render, get_object_or_404, redirect
from django.http import JsonResponse
from .models import Article

def article_list(request):
    """READ: Get all articles"""
    articles = Article.objects.filter(published=True)
    return render(request, 'articles/list.html', {'articles': articles})

def article_detail(request, pk):
    """READ: Get single article"""
    article = get_object_or_404(Article, pk=pk)
    return render(request, 'articles/detail.html', {'article': article})

def article_create(request):
    """CREATE: Add new article"""
    if request.method == 'POST':
        article = Article.objects.create(
            title=request.POST['title'],
            content=request.POST['content'],
            author_id=request.user.id
        )
        return redirect('article_detail', pk=article.pk)
    return render(request, 'articles/create.html')

def article_update(request, pk):
    """UPDATE: Modify article"""
    article = get_object_or_404(Article, pk=pk)
    if request.method == 'POST':
        article.title = request.POST['title']
        article.content = request.POST['content']
        article.save()
        return redirect('article_detail', pk=pk)
    return render(request, 'articles/edit.html', {'article': article})

def article_delete(request, pk):
    """DELETE: Remove article"""
    article = get_object_or_404(Article, pk=pk)
    if request.method == 'POST':
        article.delete()
        return redirect('article_list')
    return render(request, 'articles/confirm_delete.html', {'article': article})
```

---

### Summary: Database Integration

| Concept           | Description                         |
| ----------------- | ----------------------------------- |
| **Relational DB** | Tables, rows, columns, SQL          |
| **NoSQL**         | Flexible schema, document/key-value |
| **CRUD**          | Create, Read, Update, Delete        |
| **ORM**           | Objects map to database tables      |
| **Model**         | Python class representing a table   |
| **Migration**     | Database schema changes             |
| **QuerySet**      | Collection of database queries      |

---

## 3.9 Authentication and Authorization: Cookies, Sessions, JWT

### Authentication vs Authorization

These two concepts are often confused but serve different purposes:

| Concept            | Question           | Purpose           |
| ------------------ | ------------------ | ----------------- |
| **Authentication** | "Who are you?"     | Verify identity   |
| **Authorization**  | "What can you do?" | Check permissions |

---

### Authentication Flow

```
┌─────────────────────────────────────────────────────────┐
│               AUTHENTICATION FLOW                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   1. User submits credentials (username/password)       │
│                    ↓                                     │
│   2. Server verifies credentials against database       │
│                    ↓                                     │
│   3. If valid → Create session/token                    │
│      If invalid → Return error                          │
│                    ↓                                     │
│   4. Send session ID or token to client                 │
│                    ↓                                     │
│   5. Client stores and sends with future requests       │
│                    ↓                                     │
│   6. Server verifies session/token for each request     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### What are Cookies?

**Cookies** are small pieces of data stored by the browser and sent with every HTTP request to the same domain.

**Cookie Characteristics:**

- Stored on client (browser)
- Automatically sent with requests
- Can be read by JavaScript (unless HttpOnly)
- Have expiration dates
- Limited to ~4KB per cookie

---

### Cookie Attributes

| Attribute    | Description                       |
| ------------ | --------------------------------- |
| `name=value` | The actual data                   |
| `Expires`    | When the cookie expires           |
| `Max-Age`    | Cookie lifetime in seconds        |
| `Domain`     | Which domain can access           |
| `Path`       | Which paths can access            |
| `Secure`     | Only send over HTTPS              |
| `HttpOnly`   | JavaScript cannot access          |
| `SameSite`   | CSRF protection (Strict/Lax/None) |

---

### Setting Cookies in Django

```python
from django.http import HttpResponse

def set_cookie(request):
    response = HttpResponse("Cookie set!")

    # Set a simple cookie
    response.set_cookie('username', 'john')

    # Set cookie with options
    response.set_cookie(
        'session_id',
        'abc123xyz',
        max_age=3600,        # 1 hour
        secure=True,         # HTTPS only
        httponly=True,       # No JavaScript access
        samesite='Lax'       # CSRF protection
    )

    return response

def read_cookie(request):
    username = request.COOKIES.get('username', 'Guest')
    return HttpResponse(f"Hello, {username}!")

def delete_cookie(request):
    response = HttpResponse("Cookie deleted!")
    response.delete_cookie('username')
    return response
```

---

### What are Sessions?

**Sessions** store user data on the server, with only a session ID stored in the client's cookie.

**Session Workflow:**

```
┌─────────────────────────────────────────────────────────┐
│                 SESSION WORKFLOW                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   CLIENT                         SERVER                 │
│     │                               │                   │
│     │── Login Request ─────────────►│                   │
│     │                               │ Verify credentials│
│     │                               │ Create session    │
│     │                               │ Store: session_id │
│     │                               │   → user data     │
│     │◄── Set-Cookie: session_id ────│                   │
│     │                               │                   │
│     │── Request + Cookie ──────────►│                   │
│     │   (session_id)                │ Look up session   │
│     │                               │ Get user data     │
│     │◄── Response (personalized) ───│                   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### Session Storage Comparison

| Storage     | Location  | Pros                   | Cons                    |
| ----------- | --------- | ---------------------- | ----------------------- |
| **Cookie**  | On client | No server storage      | Size limit, less secure |
| **Session** | On server | Secure, unlimited size | Server memory/storage   |

---

### Sessions in Django

Django provides built-in session support:

```python
from django.shortcuts import render, redirect

def login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')

        # Verify credentials (simplified)
        user = authenticate(username, password)
        if user:
            # Store user info in session
            request.session['user_id'] = user.id
            request.session['username'] = user.username
            request.session['is_authenticated'] = True
            return redirect('/dashboard/')
        else:
            return render(request, 'login.html', {'error': 'Invalid credentials'})

    return render(request, 'login.html')

def dashboard(request):
    # Check if user is logged in
    if not request.session.get('is_authenticated'):
        return redirect('/login/')

    username = request.session.get('username')
    return render(request, 'dashboard.html', {'username': username})

def logout(request):
    # Clear session
    request.session.flush()
    return redirect('/login/')
```

---

### Django Authentication System

Django has a built-in authentication system:

```python
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.decorators import login_required
from django.shortcuts import render, redirect

def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']

        # Authenticate user
        user = authenticate(request, username=username, password=password)

        if user is not None:
            login(request, user)  # Creates session
            return redirect('/dashboard/')
        else:
            return render(request, 'login.html', {'error': 'Invalid credentials'})

    return render(request, 'login.html')

@login_required  # Decorator to require authentication
def dashboard_view(request):
    return render(request, 'dashboard.html', {'user': request.user})

def logout_view(request):
    logout(request)  # Clears session
    return redirect('/login/')
```

---

### What is JWT?

**JWT (JSON Web Token)** is a compact, self-contained token for securely transmitting information between parties.

**JWT Structure:**

```
┌───────────────────────────────────────────────────────┐
│                     JWT TOKEN                          │
├───────────────────────────────────────────────────────┤
│                                                       │
│   HEADER.PAYLOAD.SIGNATURE                            │
│                                                       │
│   ┌──────────┐   ┌──────────┐   ┌──────────┐         │
│   │  HEADER  │ . │ PAYLOAD  │ . │SIGNATURE │         │
│   └──────────┘   └──────────┘   └──────────┘         │
│                                                       │
│   Header: Algorithm & token type                      │
│   Payload: User data (claims)                         │
│   Signature: Verification hash                        │
│                                                       │
└───────────────────────────────────────────────────────┘

Example Token:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6ImpvaG4ifQ.
abc123signature456
```

---

### JWT Components

**1. Header:**

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**2. Payload (Claims):**

```json
{
  "user_id": 1,
  "username": "john",
  "email": "john@example.com",
  "exp": 1705500000,
  "iat": 1705496400
}
```

**3. Signature:**

```
HMACSHA256(
    base64UrlEncode(header) + "." + base64UrlEncode(payload),
    secret_key
)
```

---

### JWT Workflow

```
┌─────────────────────────────────────────────────────────┐
│                    JWT WORKFLOW                          │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   CLIENT                         SERVER                 │
│     │                               │                   │
│     │── Login (username/password) ─►│                   │
│     │                               │ Verify credentials│
│     │                               │ Generate JWT      │
│     │◄── JWT Token ─────────────────│                   │
│     │                               │                   │
│     │   (Client stores token)       │                   │
│     │                               │                   │
│     │── Request + JWT ─────────────►│                   │
│     │   (Authorization: Bearer JWT) │ Verify token      │
│     │                               │ Extract user data │
│     │◄── Response ──────────────────│                   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### Sessions vs JWT Comparison

| Aspect          | Sessions                  | JWT                              |
| --------------- | ------------------------- | -------------------------------- |
| **Storage**     | Server (database/cache)   | Client (localStorage/cookie)     |
| **Stateful**    | Yes (server stores state) | No (stateless)                   |
| **Scalability** | Need shared session store | Easy to scale (no shared state)  |
| **Size**        | Small cookie (session ID) | Larger (contains all data)       |
| **Revocation**  | Easy (delete from server) | Harder (must use blacklist)      |
| **Best For**    | Traditional web apps      | APIs, mobile apps, microservices |

---

### When to Use What?

**Use Sessions When:**

- Building traditional server-rendered web apps
- Need easy session invalidation
- User data changes frequently
- Single server or shared session store

**Use JWT When:**

- Building REST APIs
- Need stateless authentication
- Working with mobile apps
- Microservices architecture
- Cross-domain authentication needed

---

### JWT with Django REST Framework

Using `djangorestframework-simplejwt`:

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'rest_framework',
    'rest_framework_simplejwt',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

**Usage:**

```bash
# Get tokens
POST /api/token/
{
    "username": "john",
    "password": "secret"
}

# Response
{
    "access": "eyJ0eXAiOiJKV1QiLCJhbGci...",
    "refresh": "eyJ0eXAiOiJKV1QiLCJhbGci..."
}

# Use access token in requests
GET /api/protected/
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGci...
```

---

### Authorization

After authentication, **authorization** determines what the user can do.

**Types of Authorization:**
| Type | Description |
|------|-------------|
| **Role-Based (RBAC)** | Users have roles (Admin, Editor, Viewer) |
| **Permission-Based** | Users have specific permissions |
| **Attribute-Based (ABAC)** | Based on user/resource attributes |
| **Owner-Based** | Users can only access their own resources |

---

### Authorization in Django

```python
from django.contrib.auth.decorators import login_required, permission_required
from django.http import HttpResponseForbidden

# Require login
@login_required
def dashboard(request):
    return render(request, 'dashboard.html')

# Require specific permission
@permission_required('blog.add_article')
def create_article(request):
    return render(request, 'create.html')

# Custom authorization check
def edit_article(request, pk):
    article = Article.objects.get(pk=pk)

    # Check if user is the author
    if article.author != request.user:
        return HttpResponseForbidden("You can only edit your own articles")

    return render(request, 'edit.html', {'article': article})
```

---

### Summary: Authentication and Authorization

| Concept            | Description                      |
| ------------------ | -------------------------------- |
| **Authentication** | Verify user identity             |
| **Authorization**  | Check user permissions           |
| **Cookies**        | Client-side data storage         |
| **Sessions**       | Server-side state management     |
| **JWT**            | Self-contained, stateless tokens |
| **RBAC**           | Role-based access control        |

---

## 3.10 Middleware for Logging, Error Handling, and Security

### Middleware Recap

**Middleware** processes requests/responses as they flow through Django. Each middleware can:

- Inspect/modify incoming requests
- Inspect/modify outgoing responses
- Short-circuit the request (return early without calling view)
- Add headers, log information, handle errors

```
┌─────────────────────────────────────────────────────────┐
│                 MIDDLEWARE PIPELINE                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   Request   ──────────────────────────────────►  View   │
│             │         │         │         │             │
│             ▼         ▼         ▼         ▼             │
│          [MW1]     [MW2]     [MW3]     [MW4]            │
│             │         │         │         │             │
│             │         │         │         │             │
│   Response  ◄──────────────────────────────────  View   │
│             │         │         │         │             │
│             ▼         ▼         ▼         ▼             │
│          [MW4]     [MW3]     [MW2]     [MW1]            │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### Middleware Structure

```python
class MyMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        # One-time initialization

    def __call__(self, request):
        # Code before the view is called
        # (process request)

        response = self.get_response(request)

        # Code after the view is called
        # (process response)

        return response

    def process_exception(self, request, exception):
        # Called if view raises an exception
        pass
```

---

### Logging Middleware

**Logging** helps track application behavior, debug issues, and monitor performance.

#### Simple Request Logger

```python
import logging
import time

logger = logging.getLogger(__name__)

class RequestLoggingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Log request details
        start_time = time.time()

        logger.info(f"Request: {request.method} {request.path}")
        logger.info(f"User: {request.user}")
        logger.info(f"IP: {self.get_client_ip(request)}")

        response = self.get_response(request)

        # Log response details
        duration = time.time() - start_time
        logger.info(f"Response: {response.status_code} ({duration:.2f}s)")

        return response

    def get_client_ip(self, request):
        x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
        if x_forwarded_for:
            return x_forwarded_for.split(',')[0]
        return request.META.get('REMOTE_ADDR')
```

---

#### Advanced Request Logger

```python
import logging
import json
from datetime import datetime

logger = logging.getLogger('request_logger')

class AdvancedLoggingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Create log entry
        log_data = {
            'timestamp': datetime.now().isoformat(),
            'method': request.method,
            'path': request.path,
            'query_params': dict(request.GET),
            'user': str(request.user),
            'ip': self.get_client_ip(request),
            'user_agent': request.META.get('HTTP_USER_AGENT', ''),
        }

        # Don't log sensitive data
        if request.method == 'POST' and 'password' not in request.path:
            log_data['body_keys'] = list(request.POST.keys())

        start_time = datetime.now()
        response = self.get_response(request)

        # Add response info
        log_data['status_code'] = response.status_code
        log_data['duration_ms'] = (datetime.now() - start_time).total_seconds() * 1000

        # Log as JSON for easy parsing
        logger.info(json.dumps(log_data))

        return response

    def get_client_ip(self, request):
        x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
        if x_forwarded_for:
            return x_forwarded_for.split(',')[0]
        return request.META.get('REMOTE_ADDR')
```

---

#### Configure Logging in settings.py

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {message}',
            'style': '{',
        },
    },
    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': 'logs/requests.log',
            'formatter': 'verbose',
        },
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'verbose',
        },
    },
    'loggers': {
        'request_logger': {
            'handlers': ['file', 'console'],
            'level': 'INFO',
        },
    },
}
```

---

### Error Handling Middleware

**Error handling** middleware catches exceptions and provides custom error responses.

#### Basic Error Handler

```python
import logging
import traceback
from django.http import JsonResponse, HttpResponse

logger = logging.getLogger(__name__)

class ErrorHandlingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        return self.get_response(request)

    def process_exception(self, request, exception):
        # Log the error
        logger.error(f"Error: {str(exception)}")
        logger.error(traceback.format_exc())

        # Return custom error response
        if request.path.startswith('/api/'):
            # JSON response for API
            return JsonResponse({
                'error': 'An error occurred',
                'message': str(exception)
            }, status=500)
        else:
            # HTML response for web pages
            return HttpResponse(
                "An error occurred. Please try again.",
                status=500
            )
```

---

#### Custom Error Pages

```python
from django.shortcuts import render

class CustomErrorMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)

        # Custom 404 page
        if response.status_code == 404:
            return render(request, 'errors/404.html', status=404)

        # Custom 500 page
        if response.status_code == 500:
            return render(request, 'errors/500.html', status=500)

        return response

    def process_exception(self, request, exception):
        # Log error
        import logging
        logging.error(f"Exception: {exception}", exc_info=True)

        # Return custom error page
        return render(request, 'errors/500.html', status=500)
```

---

#### Error Notification

```python
import logging
from django.core.mail import mail_admins

logger = logging.getLogger(__name__)

class ErrorNotificationMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        return self.get_response(request)

    def process_exception(self, request, exception):
        # Log the error
        error_message = f"""
        Error: {str(exception)}
        Path: {request.path}
        Method: {request.method}
        User: {request.user}
        """

        logger.error(error_message, exc_info=True)

        # Send email to admins (in production)
        # mail_admins(
        #     subject='Server Error',
        #     message=error_message
        # )

        return None  # Let Django handle the response
```

---

### Security Middleware

**Security middleware** protects the application from common attacks.

#### Security Headers Middleware

```python
class SecurityHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)

        # Content Security Policy - prevents XSS
        response['Content-Security-Policy'] = "default-src 'self'"

        # Prevent MIME type sniffing
        response['X-Content-Type-Options'] = 'nosniff'

        # Prevent clickjacking
        response['X-Frame-Options'] = 'DENY'

        # Enable XSS filter
        response['X-XSS-Protection'] = '1; mode=block'

        # Referrer policy
        response['Referrer-Policy'] = 'strict-origin-when-cross-origin'

        # HSTS (HTTPS only)
        response['Strict-Transport-Security'] = 'max-age=31536000; includeSubDomains'

        return response
```

---

#### Rate Limiting Middleware

```python
import time
from django.http import HttpResponseTooManyRequests
from django.core.cache import cache

class RateLimitMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        self.rate_limit = 100  # requests per minute
        self.window = 60  # seconds

    def __call__(self, request):
        # Get client identifier
        client_ip = self.get_client_ip(request)
        cache_key = f'rate_limit:{client_ip}'

        # Get current request count
        request_count = cache.get(cache_key, 0)

        if request_count >= self.rate_limit:
            return HttpResponseTooManyRequests(
                'Rate limit exceeded. Please try again later.'
            )

        # Increment counter
        cache.set(cache_key, request_count + 1, self.window)

        response = self.get_response(request)

        # Add rate limit headers
        response['X-RateLimit-Limit'] = str(self.rate_limit)
        response['X-RateLimit-Remaining'] = str(self.rate_limit - request_count - 1)

        return response

    def get_client_ip(self, request):
        x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
        if x_forwarded_for:
            return x_forwarded_for.split(',')[0]
        return request.META.get('REMOTE_ADDR')
```

---

#### IP Blocking Middleware

```python
from django.http import HttpResponseForbidden

class IPBlockMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        self.blocked_ips = [
            '192.168.1.100',
            '10.0.0.50',
        ]

    def __call__(self, request):
        client_ip = self.get_client_ip(request)

        if client_ip in self.blocked_ips:
            return HttpResponseForbidden('Access denied')

        return self.get_response(request)

    def get_client_ip(self, request):
        x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
        if x_forwarded_for:
            return x_forwarded_for.split(',')[0]
        return request.META.get('REMOTE_ADDR')
```

---

#### Maintenance Mode Middleware

```python
from django.http import HttpResponse
from django.conf import settings

class MaintenanceModeMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Check if maintenance mode is enabled
        if getattr(settings, 'MAINTENANCE_MODE', False):
            # Allow admin access
            if request.path.startswith('/admin/'):
                return self.get_response(request)

            # Allow specific IPs
            allowed_ips = getattr(settings, 'MAINTENANCE_ALLOWED_IPS', [])
            client_ip = self.get_client_ip(request)
            if client_ip in allowed_ips:
                return self.get_response(request)

            # Return maintenance page
            return HttpResponse(
                "<h1>Site Under Maintenance</h1>"
                "<p>We'll be back soon!</p>",
                status=503
            )

        return self.get_response(request)

    def get_client_ip(self, request):
        x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
        if x_forwarded_for:
            return x_forwarded_for.split(',')[0]
        return request.META.get('REMOTE_ADDR')
```

---

### Registering Custom Middleware

Add middleware to `settings.py`:

```python
MIDDLEWARE = [
    # Django built-in middleware
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',

    # Custom middleware (order matters!)
    'myapp.middleware.RequestLoggingMiddleware',
    'myapp.middleware.ErrorHandlingMiddleware',
    'myapp.middleware.SecurityHeadersMiddleware',
    'myapp.middleware.RateLimitMiddleware',
]
```

---

### Middleware Order Importance

| Position   | Middleware Type   | Reason                             |
| ---------- | ----------------- | ---------------------------------- |
| **First**  | Security, Logging | Catches all requests               |
| **Middle** | Session, Auth     | Needs security checks done         |
| **Last**   | Error Handling    | Catches errors from all middleware |

---

### Complete Middleware Example

A middleware that combines logging, timing, and basic security:

```python
import logging
import time
from django.http import HttpResponseForbidden

logger = logging.getLogger(__name__)

class ComprehensiveMiddleware:
    """
    Middleware that provides:
    - Request logging
    - Response timing
    - Basic security checks
    """

    def __init__(self, get_response):
        self.get_response = get_response
        self.blocked_paths = ['/admin/secret/', '/internal/']

    def __call__(self, request):
        # Security check
        if any(request.path.startswith(p) for p in self.blocked_paths):
            if not request.user.is_superuser:
                logger.warning(f"Blocked access to {request.path} by {request.user}")
                return HttpResponseForbidden("Access denied")

        # Log request
        start_time = time.time()
        logger.info(f"→ {request.method} {request.path}")

        # Call view
        response = self.get_response(request)

        # Log response with timing
        duration = (time.time() - start_time) * 1000
        logger.info(f"← {response.status_code} ({duration:.2f}ms)")

        # Add custom header
        response['X-Response-Time'] = f"{duration:.2f}ms"

        return response

    def process_exception(self, request, exception):
        logger.error(f"Exception in {request.path}: {exception}", exc_info=True)
        return None
```

---

### Summary: Middleware for Logging, Error Handling, and Security

| Middleware Type      | Purpose                              |
| -------------------- | ------------------------------------ |
| **Logging**          | Track requests, users, timing        |
| **Error Handling**   | Catch exceptions, custom error pages |
| **Security Headers** | Add protective HTTP headers          |
| **Rate Limiting**    | Prevent abuse                        |
| **IP Blocking**      | Block malicious IPs                  |
| **Maintenance Mode** | Show maintenance page                |

---

### Best Practices

1. **Keep middleware simple** — do one thing well
2. **Order matters** — security before authentication, logging first
3. **Don't block** — avoid slow operations in middleware
4. **Log appropriately** — don't log sensitive data
5. **Test thoroughly** — middleware affects every request
6. **Use built-in** — Django's middleware for common tasks

---

## Course Summary

### What We Learned

| Section  | Topic                | Key Concepts                                            |
| -------- | -------------------- | ------------------------------------------------------- |
| **3.1**  | MVC Architecture     | Model, View, Controller separation                      |
| **3.2**  | Backend Role         | Server logic, routing, business logic, security         |
| **3.3**  | Django Framework     | MTV pattern, project structure, apps                    |
| **3.4**  | Requests/Responses   | HTTP methods, views, JSON responses                     |
| **3.5**  | Forms & Sessions     | POST/GET handling, CSRF, session management             |
| **3.6**  | Routing & Templates  | URL dispatcher, middleware, DTL                         |
| **3.7**  | Framework Comparison | Django, Flask, FastAPI, ASP.NET, Rails, Spring, Express |
| **3.8**  | Database Integration | SQL vs NoSQL, CRUD, Django ORM                          |
| **3.9**  | Authentication       | Cookies, sessions, JWT, authorization                   |
| **3.10** | Middleware           | Logging, error handling, security                       |

---

_End of Unit 3: Backend Web Development_
