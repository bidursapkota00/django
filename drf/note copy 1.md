# Chapter 4: Web Services and APIs

This chapter explores how modern web applications communicate with each other through APIs (Application Programming Interfaces). APIs are the backbone of today's interconnected digital ecosystem, enabling everything from mobile apps to complex enterprise systems to share data and functionality seamlessly.

---

## 4.1 API Basics: Role in Web Applications

### What is an API?

An **API (Application Programming Interface)** is a set of rules, protocols, and tools that allows different software applications to communicate with each other. Think of an API as a waiter in a restaurant: you (the client) tell the waiter (the API) what you want, and the waiter communicates your order to the kitchen (the server) and brings back your food (the response).

In technical terms, an API defines:

- **What operations are available** (e.g., get user data, create a post, delete a record)
- **How to request those operations** (e.g., what URL to call, what data to send)
- **What format the response will be in** (e.g., JSON, XML)

APIs act as intermediaries that enable software components to interact without needing to know the internal workings of each other. This abstraction is powerful because it allows developers to use complex services without understanding their complete implementation.

### Types of APIs

There are several types of APIs based on their accessibility and purpose:

#### 1. Open APIs (Public APIs)

Open APIs are publicly available and can be used by any developer. They often require registration and an API key for access control and rate limiting. Examples include:

- Twitter API for accessing tweets and user data
- Google Maps API for embedding maps and location services
- OpenWeatherMap API for weather information

#### 2. Internal APIs (Private APIs)

Internal APIs are used within an organization to connect different internal systems. They are not exposed to external developers and are designed to improve productivity and data sharing among internal teams. For example, a company might have an internal API that connects their HR system with their payroll system.

#### 3. Partner APIs

Partner APIs are shared with specific business partners under agreed terms. They require special authorization and are used for B2B (business-to-business) integrations. An example would be an e-commerce platform sharing an API with its payment processor.

#### 4. Composite APIs

Composite APIs combine multiple API calls into a single request. They are useful when a client needs data from several sources. Instead of making five separate API calls, a composite API can bundle them together, reducing network overhead and improving performance.

### Why APIs are Used

APIs serve numerous critical purposes in modern software development:

#### 1. Modularity and Separation of Concerns

APIs allow developers to build modular systems where each component has a specific responsibility. The frontend can focus on user interface and experience, while the backend handles data processing and business logic. This separation makes systems easier to develop, test, and maintain.

#### 2. Reusability

Once an API is built, it can be used by multiple clients. A single backend API can serve a web application, a mobile app, a desktop application, and even third-party integrations. This eliminates code duplication and ensures consistency across platforms.

#### 3. Scalability

APIs enable horizontal scaling. As demand grows, you can deploy multiple instances of your API server behind a load balancer. Each API endpoint can also be scaled independently based on its usage patterns.

#### 4. Integration

APIs make it possible to integrate with external services without building everything from scratch. Need payment processing? Use Stripe's API. Need email delivery? Use SendGrid's API. Need maps? Use Google Maps API. This dramatically accelerates development.

#### 5. Security and Access Control

APIs provide a controlled way to expose functionality. Instead of giving direct database access (which would be extremely dangerous), you expose specific endpoints with defined permissions. This allows fine-grained control over who can access what data and what operations they can perform.

#### 6. Platform Independence

APIs abstract away implementation details. A client written in JavaScript can communicate with a server written in Python, which might store data in a PostgreSQL database. The client doesn't need to know anything about Python or PostgreSQL—it just needs to know how to make HTTP requests.

### Browser-Server Communication

Understanding browser-server communication is fundamental to understanding how APIs work in web applications.

#### The Request-Response Cycle

When you type a URL in your browser or click a link, a series of events occurs:

1. **DNS Resolution**: The browser converts the domain name (e.g., www.example.com) to an IP address using the Domain Name System.

2. **TCP Connection**: The browser establishes a TCP (Transmission Control Protocol) connection with the server. For HTTPS, this includes a TLS handshake for encryption.

3. **HTTP Request**: The browser sends an HTTP request to the server. This request includes:
   - The HTTP method (GET, POST, PUT, DELETE, etc.)
   - The URL path
   - Headers (metadata about the request)
   - Body (optional, containing data for POST/PUT requests)

4. **Server Processing**: The server receives the request, processes it (which might involve querying a database, performing calculations, calling other services), and prepares a response.

5. **HTTP Response**: The server sends back an HTTP response containing:
   - A status code (200 for success, 404 for not found, 500 for server error, etc.)
   - Headers (metadata about the response)
   - Body (the actual content—HTML, JSON, XML, images, etc.)

6. **Rendering**: The browser receives the response and renders it. For HTML, it parses the document and displays it. For API responses (typically JSON), the JavaScript code processes the data.

#### Synchronous vs Asynchronous Communication

**Synchronous communication** blocks the execution until a response is received. In early web development, page loads were synchronous—clicking a link would freeze the browser until the new page loaded.

**Asynchronous communication** allows the page to remain interactive while waiting for responses. Modern web applications use AJAX (Asynchronous JavaScript and XML) and the Fetch API to make asynchronous requests. This enables dynamic updates without full page reloads.

#### The Role of APIs in Modern Architecture

In traditional web applications (Multi-Page Applications or MPAs), the server generates complete HTML pages. When you click a link, the entire page reloads.

In modern web applications (Single-Page Applications or SPAs), the initial page load fetches a JavaScript application. Subsequent interactions are handled by the JavaScript code, which makes API calls to fetch or send data. Only the necessary parts of the page are updated, creating a smoother user experience.

This architectural shift has made APIs central to web development. The backend becomes an "API server" that serves data (usually as JSON), and the frontend becomes a separate application that consumes this data.

### API in Practice: How It All Connects

Consider a social media application:

1. **User opens the app**: The browser loads the frontend (HTML, CSS, JavaScript).

2. **User logs in**: The frontend sends the username and password to the `/api/login` endpoint. The server validates credentials and returns an authentication token.

3. **Loading the feed**: The frontend calls `/api/posts?page=1`. The server queries the database for recent posts and returns them as JSON.

4. **Creating a post**: The user writes a post and clicks "Share." The frontend sends the post content to `/api/posts` using a POST request. The server saves the post and returns the created post data.

5. **Liking a post**: The user clicks the like button. The frontend sends a POST request to `/api/posts/123/like`. The server updates the like count and returns the new count.

Each of these interactions happens through API calls, with the frontend and backend communicating via HTTP requests and JSON responses.

### Complete Code Example: Simple API Client (Browser)

The following is a complete HTML file that demonstrates how a browser communicates with an API. It uses a free public API to fetch and display data.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>API Communication Demo</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        max-width: 800px;
        margin: 50px auto;
        padding: 20px;
      }
      .user-card {
        border: 1px solid #ddd;
        padding: 15px;
        margin: 10px 0;
        border-radius: 8px;
      }
      button {
        background-color: #007bff;
        color: white;
        border: none;
        padding: 10px 20px;
        cursor: pointer;
        border-radius: 5px;
      }
      button:hover {
        background-color: #0056b3;
      }
      #loading {
        color: #666;
        font-style: italic;
      }
    </style>
  </head>
  <body>
    <h1>Browser-Server API Communication</h1>
    <p>Click the button to fetch user data from a public API.</p>

    <button id="fetchBtn">Fetch Users from API</button>
    <p id="loading" style="display: none;">Loading data from server...</p>

    <div id="users"></div>

    <script>
      // Get references to DOM elements
      const fetchBtn = document.getElementById("fetchBtn");
      const usersDiv = document.getElementById("users");
      const loadingText = document.getElementById("loading");

      // Add click event listener to the button
      fetchBtn.addEventListener("click", function () {
        // Show loading indicator
        loadingText.style.display = "block";
        usersDiv.innerHTML = "";

        // Make an API request using the Fetch API
        // This is an asynchronous HTTP GET request
        fetch("https://jsonplaceholder.typicode.com/users")
          .then(function (response) {
            // Check if the response was successful
            if (!response.ok) {
              throw new Error(
                "Network response was not ok: " + response.status,
              );
            }
            // Parse the JSON from the response body
            return response.json();
          })
          .then(function (users) {
            // Hide loading indicator
            loadingText.style.display = "none";

            // Display each user
            users.forEach(function (user) {
              const card = document.createElement("div");
              card.className = "user-card";
              card.innerHTML =
                "<h3>" +
                user.name +
                "</h3>" +
                "<p><strong>Email:</strong> " +
                user.email +
                "</p>" +
                "<p><strong>Company:</strong> " +
                user.company.name +
                "</p>" +
                "<p><strong>City:</strong> " +
                user.address.city +
                "</p>";
              usersDiv.appendChild(card);
            });
          })
          .catch(function (error) {
            // Handle any errors that occurred during the fetch
            loadingText.style.display = "none";
            usersDiv.innerHTML =
              '<p style="color: red;">Error: ' + error.message + "</p>";
          });
      });
    </script>
  </body>
</html>
```

**How to run this example:**

1. Save the code above as `api_demo.html`
2. Open the file in any web browser
3. Click the "Fetch Users from API" button
4. The browser will make an HTTP GET request to the JSONPlaceholder API and display the returned user data

This example demonstrates the complete request-response cycle: the browser sends a request, waits asynchronously for the response, parses the JSON data, and updates the page dynamically.

---

## 4.2 REST Principles and Design, RESTful APIs

### What is REST?

**REST (Representational State Transfer)** is an architectural style for designing networked applications. It was introduced by Roy Fielding in his doctoral dissertation in 2000. REST is not a protocol or a standard—it's a set of architectural constraints that, when applied to web services, create systems that are scalable, flexible, and easy to understand.

A **RESTful API** is an API that adheres to REST principles. It uses HTTP as its communication protocol and follows specific conventions for how resources are identified, accessed, and manipulated.

REST has become the dominant architectural style for web APIs because it leverages the existing infrastructure of the web (HTTP, URLs, caching mechanisms) and provides a intuitive way to model operations on data.

### The Six Constraints of REST

Roy Fielding defined six architectural constraints that characterize REST:

#### 1. Client-Server Architecture

The client and server must be separate and independent. The client handles the user interface and user experience. The server handles data storage, business logic, and security. This separation allows each to evolve independently—you can completely rewrite the frontend without touching the backend, and vice versa.

**Benefits:**

- Portability of the user interface across multiple platforms
- Scalability by simplifying server components
- Independent evolution of client and server

#### 2. Statelessness

Each request from the client must contain all the information needed to process that request. The server does not store any client state between requests. If authentication is required, the client must include authentication credentials (like a token) with every request.

**Why statelessness matters:**

- **Scalability**: Any server can handle any request because there's no session state to maintain
- **Reliability**: If a server fails, requests can be routed to another server without losing state
- **Simplicity**: The server doesn't need complex session management logic

**Example of statelessness:**

```
Request 1: GET /api/users/123
Headers: Authorization: Bearer abc123token

Request 2: GET /api/users/123/posts
Headers: Authorization: Bearer abc123token
```

Each request includes the authentication token. The server doesn't remember that Request 1 was authenticated—the client must prove its identity every time.

#### 3. Cacheability

Responses must explicitly or implicitly define themselves as cacheable or non-cacheable. If cacheable, clients can reuse response data for equivalent requests, reducing server load and improving performance.

HTTP provides caching mechanisms through headers like:

- `Cache-Control`: Specifies caching directives
- `Expires`: Specifies when the response becomes stale
- `ETag`: A version identifier for the resource

**Example cache headers:**

```
HTTP/1.1 200 OK
Cache-Control: max-age=3600
ETag: "abc123"
```

This tells the client it can cache the response for 3600 seconds (1 hour).

#### 4. Uniform Interface

This is the fundamental constraint that distinguishes REST from other architectural styles. A uniform interface simplifies and decouples the architecture, allowing each part to evolve independently.

The uniform interface has four sub-constraints:

**a) Identification of Resources**: Resources are identified by URIs (Uniform Resource Identifiers). A resource could be a user, a product, an order, or any other entity. Each resource has a unique URI.

**b) Manipulation of Resources Through Representations**: When a client holds a representation of a resource (like a JSON object), it has enough information to modify or delete that resource.

**c) Self-Descriptive Messages**: Each message (request or response) contains enough information to describe how to process it. This includes the content type, caching headers, and status codes.

**d) Hypermedia as the Engine of Application State (HATEOAS)**: Responses should include links to related resources and available actions. The client can navigate the API by following these links.

#### 5. Layered System

The architecture can be composed of hierarchical layers, with each layer only knowing about the layer immediately adjacent to it. A client cannot tell whether it's connected directly to the end server or an intermediary. This allows for:

- **Load balancers**: Distributing requests across multiple servers
- **Proxies**: Caching responses or filtering content
- **Gateways**: Translating between protocols or handling authentication

#### 6. Code on Demand (Optional)

Servers can extend client functionality by sending executable code (like JavaScript). This is the only optional constraint in REST. While it adds flexibility, it also complicates security and is not commonly used in typical REST APIs.

### HTTP Verbs (Methods)

REST APIs use standard HTTP methods to define actions on resources. Each method has specific semantics:

#### GET - Retrieve Resources

GET requests retrieve data without modifying it. They are **safe** (don't change server state) and **idempotent** (calling multiple times has the same effect as calling once).

**Use cases:**

- Fetching a list of users: `GET /api/users`
- Fetching a specific user: `GET /api/users/123`
- Searching with filters: `GET /api/users?role=admin&active=true`

**Characteristics:**

- Should never modify data
- Can be cached
- Can be bookmarked
- Parameters go in the URL

#### POST - Create Resources

POST requests create new resources. They are neither safe nor idempotent—each POST typically creates a new resource.

**Use cases:**

- Creating a new user: `POST /api/users`
- Submitting a form: `POST /api/contact`
- Uploading a file: `POST /api/uploads`

**Characteristics:**

- Creates new resources
- Request body contains the resource data
- Returns the created resource (usually with its new ID)
- Typically returns 201 (Created) status code

#### PUT - Update/Replace Resources

PUT requests replace an existing resource entirely. They are idempotent—making the same PUT request multiple times has the same effect as making it once.

**Use cases:**

- Updating a user: `PUT /api/users/123`
- Replacing a configuration: `PUT /api/settings/notifications`

**Characteristics:**

- Replaces the entire resource
- Client must send the complete resource
- If the resource doesn't exist, it may create it
- Idempotent

#### PATCH - Partial Update

PATCH requests partially modify a resource. Unlike PUT, you only send the fields that need to change.

**Use cases:**

- Updating only the email: `PATCH /api/users/123` with body `{"email": "new@email.com"}`
- Changing the status: `PATCH /api/orders/456` with body `{"status": "shipped"}`

**Characteristics:**

- Partial update
- Only changed fields in the request body
- More efficient for small updates

#### DELETE - Remove Resources

DELETE requests remove a resource. They are idempotent—deleting a resource that doesn't exist should return the same response as successfully deleting it.

**Use cases:**

- Deleting a user: `DELETE /api/users/123`
- Removing an item from cart: `DELETE /api/cart/items/789`

**Characteristics:**

- Removes the resource
- Should be idempotent
- Typically returns 204 (No Content) or 200 with a confirmation message

### Endpoints and URL Design

Good URL design is crucial for creating intuitive and maintainable APIs. Here are the key principles:

#### 1. Use Nouns, Not Verbs

URLs should represent resources (nouns), and HTTP methods should represent actions (verbs).

**Good:**

- `GET /users` - Get all users
- `POST /users` - Create a user
- `DELETE /users/123` - Delete user 123

**Bad:**

- `GET /getUsers`
- `POST /createUser`
- `GET /deleteUser/123`

#### 2. Use Plural Nouns

Use plural nouns for consistency, even when referring to a single resource.

**Good:**

- `/users`
- `/users/123`
- `/posts`
- `/posts/456`

**Avoid mixing:**

- `/user` for single, `/users` for collection

#### 3. Hierarchical Relationships

Use URL hierarchy to represent relationships between resources.

- `GET /users/123/posts` - All posts by user 123
- `GET /users/123/posts/456` - Post 456 by user 123
- `GET /posts/456/comments` - All comments on post 456

#### 4. Use Query Parameters for Filtering

Use query strings for filtering, sorting, and pagination:

- `GET /users?role=admin` - Filter by role
- `GET /users?sort=name&order=asc` - Sort results
- `GET /users?page=2&limit=20` - Pagination
- `GET /products?minPrice=10&maxPrice=100` - Range filter

#### 5. Use Consistent Naming Conventions

Choose a naming convention and stick to it:

- **Lowercase**: `/users`, `/blog-posts`
- **Hyphens for multi-word**: `/user-profiles`, `/order-items`
- **Avoid underscores**: `/user_profiles` (less readable in URLs)

### Resource Modeling

Resource modeling is the process of identifying and designing the resources in your API. Good resource modeling leads to intuitive, usable APIs.

#### Identifying Resources

Resources are the fundamental units of your API. They typically map to:

- Database entities (users, products, orders)
- Business concepts (shopping cart, checkout, payment)
- Actions that behave like resources (authentication, search results)

#### Resource Relationships

Resources often have relationships with each other:

**One-to-Many:**

- User has many Posts
- `/users/123/posts`

**Many-to-Many:**

- Users belong to many Groups, Groups have many Users
- `/users/123/groups` or `/groups/456/members`

**Nested vs. Flat:**

**Nested approach:**

```
GET /authors/123/books/456/chapters/789
```

**Flat approach:**

```
GET /chapters/789
```

The nested approach shows the hierarchy but can become unwieldy. A common practice is to limit nesting to one or two levels, using query parameters for deeper filtering.

### HTTP Status Codes

Status codes communicate the result of an API request. They are grouped into categories:

#### 2xx - Success

- **200 OK**: Request succeeded. Used for GET, PUT, PATCH when returning data.
- **201 Created**: Resource created successfully. Used for POST.
- **204 No Content**: Request succeeded but no content to return. Used for DELETE.

#### 3xx - Redirection

- **301 Moved Permanently**: Resource has moved to a new URL permanently.
- **304 Not Modified**: Cached version is still valid (for conditional requests).

#### 4xx - Client Errors

- **400 Bad Request**: Malformed request (invalid JSON, missing required fields).
- **401 Unauthorized**: Authentication required or invalid credentials.
- **403 Forbidden**: Authenticated but not authorized for this resource.
- **404 Not Found**: Resource doesn't exist.
- **405 Method Not Allowed**: HTTP method not supported for this endpoint.
- **422 Unprocessable Entity**: Request is well-formed but contains semantic errors.
- **429 Too Many Requests**: Rate limit exceeded.

#### 5xx - Server Errors

- **500 Internal Server Error**: Generic server error.
- **502 Bad Gateway**: Server received an invalid response from upstream server.
- **503 Service Unavailable**: Server is temporarily unavailable.

### API Structure Best Practices

#### Versioning

APIs evolve over time. Versioning allows you to make changes without breaking existing clients.

**URL versioning:**

```
/api/v1/users
/api/v2/users
```

**Header versioning:**

```
GET /api/users
Headers: Api-Version: 2
```

**Query parameter versioning:**

```
/api/users?version=2
```

URL versioning is the most common and explicit approach.

#### Consistent Response Format

Maintain a consistent response structure across all endpoints:

```json
{
  "success": true,
  "data": {
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com"
  },
  "message": null
}
```

For errors:

```json
{
  "success": false,
  "data": null,
  "message": "User not found",
  "errors": [
    {
      "field": "id",
      "message": "No user exists with id 999"
    }
  ]
}
```

#### Pagination

For large collections, always implement pagination:

```json
{
    "data": [...],
    "pagination": {
        "currentPage": 2,
        "totalPages": 10,
        "totalItems": 195,
        "itemsPerPage": 20
    }
}
```

#### Authentication

Common authentication approaches:

- **API Keys**: Simple but less secure. Include in headers.
- **Bearer Tokens (JWT)**: More secure. Include in Authorization header.
- **OAuth 2.0**: For third-party access and delegated permissions.

### Complete Code Example: RESTful API with Django REST Framework

The following example demonstrates how to create a complete RESTful API using Django REST Framework (DRF). This example includes all the files needed to run a working Books API with all HTTP verbs.

#### Step 1: Project Setup

First, create a new Django project and install required packages:

```bash
# Create a project directory and navigate into it
mkdir books_api
cd books_api

# Create a virtual environment and activate it
python -m venv venv
venv\Scripts\activate  # On Windows
# source venv/bin/activate  # On Linux/Mac

# Install Django and Django REST Framework
pip install django djangorestframework

# Create Django project
django-admin startproject config .

# Create an app for books
python manage.py startapp books
```

#### Step 2: Configure Settings (config/settings.py)

Add `rest_framework` and `books` to the installed apps:

```python
# config/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # Third-party apps
    'rest_framework',
    # Local apps
    'books',
]

# Optional: Configure REST Framework settings
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ]
}
```

#### Step 3: Create the Model (books/models.py)

Define the Book model that represents our resource:

```python
# books/models.py

from django.db import models


class Book(models.Model):
    """
    Book model representing a book resource.
    Each book has a title, author, and optional publication year.
    """
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    year = models.IntegerField(null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        ordering = ['id']

    def __str__(self):
        return f"{self.title} by {self.author}"
```

#### Step 4: Create the Serializer (books/serializers.py)

Serializers convert model instances to JSON and validate incoming data:

```python
# books/serializers.py

from rest_framework import serializers
from .models import Book


class BookSerializer(serializers.ModelSerializer):
    """
    Serializer for the Book model.
    Handles conversion between Book instances and JSON.
    Also performs validation on incoming data.
    """
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'year', 'created_at', 'updated_at']
        read_only_fields = ['id', 'created_at', 'updated_at']

    def validate_year(self, value):
        """Custom validation for the year field."""
        if value is not None and (value < 1000 or value > 2100):
            raise serializers.ValidationError("Year must be between 1000 and 2100.")
        return value

    def validate_title(self, value):
        """Ensure title is not empty after stripping whitespace."""
        if not value.strip():
            raise serializers.ValidationError("Title cannot be empty.")
        return value.strip()
```

#### Step 5: Create the Views (books/views.py)

DRF provides ViewSets that automatically handle all CRUD operations:

```python
# books/views.py

from rest_framework import viewsets, status
from rest_framework.response import Response
from rest_framework.decorators import api_view
from .models import Book
from .serializers import BookSerializer


class BookViewSet(viewsets.ModelViewSet):
    """
    ViewSet for the Book model.

    This single class provides all CRUD operations:
    - GET /api/books/ - List all books
    - POST /api/books/ - Create a new book
    - GET /api/books/{id}/ - Retrieve a specific book
    - PUT /api/books/{id}/ - Update a book (full replacement)
    - PATCH /api/books/{id}/ - Partial update of a book
    - DELETE /api/books/{id}/ - Delete a book
    """
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def list(self, request):
        """
        GET /api/books/
        Returns a list of all books.
        Supports filtering by author using query parameter.
        """
        queryset = self.get_queryset()

        # Filter by author if provided
        author = request.query_params.get('author', None)
        if author:
            queryset = queryset.filter(author__icontains=author)

        serializer = self.get_serializer(queryset, many=True)
        return Response({
            'success': True,
            'count': queryset.count(),
            'data': serializer.data
        })

    def retrieve(self, request, pk=None):
        """
        GET /api/books/{id}/
        Returns a single book by ID.
        """
        try:
            book = self.get_object()
            serializer = self.get_serializer(book)
            return Response({
                'success': True,
                'data': serializer.data
            })
        except Book.DoesNotExist:
            return Response({
                'success': False,
                'message': 'Book not found'
            }, status=status.HTTP_404_NOT_FOUND)

    def create(self, request):
        """
        POST /api/books/
        Creates a new book.
        """
        serializer = self.get_serializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response({
                'success': True,
                'message': 'Book created successfully',
                'data': serializer.data
            }, status=status.HTTP_201_CREATED)
        return Response({
            'success': False,
            'message': 'Validation failed',
            'errors': serializer.errors
        }, status=status.HTTP_400_BAD_REQUEST)

    def update(self, request, pk=None):
        """
        PUT /api/books/{id}/
        Updates a book (full replacement).
        """
        book = self.get_object()
        serializer = self.get_serializer(book, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response({
                'success': True,
                'message': 'Book updated successfully',
                'data': serializer.data
            })
        return Response({
            'success': False,
            'message': 'Validation failed',
            'errors': serializer.errors
        }, status=status.HTTP_400_BAD_REQUEST)

    def partial_update(self, request, pk=None):
        """
        PATCH /api/books/{id}/
        Partially updates a book.
        """
        book = self.get_object()
        serializer = self.get_serializer(book, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response({
                'success': True,
                'message': 'Book partially updated',
                'data': serializer.data
            })
        return Response({
            'success': False,
            'message': 'Validation failed',
            'errors': serializer.errors
        }, status=status.HTTP_400_BAD_REQUEST)

    def destroy(self, request, pk=None):
        """
        DELETE /api/books/{id}/
        Deletes a book.
        """
        book = self.get_object()
        book.delete()
        return Response({
            'success': True,
            'message': 'Book deleted successfully'
        }, status=status.HTTP_200_OK)


@api_view(['GET'])
def api_root(request):
    """
    API root endpoint providing documentation of available endpoints.
    """
    return Response({
        'message': 'Welcome to the Books API',
        'endpoints': {
            'GET /api/books/': 'List all books',
            'POST /api/books/': 'Create a new book',
            'GET /api/books/{id}/': 'Get a specific book',
            'PUT /api/books/{id}/': 'Update a book (full)',
            'PATCH /api/books/{id}/': 'Update a book (partial)',
            'DELETE /api/books/{id}/': 'Delete a book',
        }
    })
```

#### Alternative: Function-Based Views (books/views.py)

The same API functionality can be achieved using function-based views with DRF's `@api_view` decorator. This approach is simpler for beginners and gives more explicit control over each operation.

```python
# books/views.py (Function-Based Views Alternative)

from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Book
from .serializers import BookSerializer


@api_view(['GET'])
def api_root(request):
    """
    API root endpoint providing documentation.
    """
    return Response({
        'message': 'Welcome to the Books API',
        'endpoints': {
            'GET /api/books/': 'List all books',
            'POST /api/books/': 'Create a new book',
            'GET /api/books/{id}/': 'Get a specific book',
            'PUT /api/books/{id}/': 'Update a book (full)',
            'PATCH /api/books/{id}/': 'Update a book (partial)',
            'DELETE /api/books/{id}/': 'Delete a book',
        }
    })


@api_view(['GET', 'POST'])
def book_list(request):
    """
    GET: List all books (with optional author filter)
    POST: Create a new book
    """
    if request.method == 'GET':
        # Get all books
        books = Book.objects.all()

        # Filter by author if provided
        author = request.query_params.get('author', None)
        if author:
            books = books.filter(author__icontains=author)

        # Serialize the queryset
        serializer = BookSerializer(books, many=True)
        return Response({
            'success': True,
            'count': books.count(),
            'data': serializer.data
        })

    elif request.method == 'POST':
        # Create a new book
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response({
                'success': True,
                'message': 'Book created successfully',
                'data': serializer.data
            }, status=status.HTTP_201_CREATED)
        return Response({
            'success': False,
            'message': 'Validation failed',
            'errors': serializer.errors
        }, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'PUT', 'PATCH', 'DELETE'])
def book_detail(request, pk):
    """
    GET: Retrieve a single book
    PUT: Update a book (full replacement)
    PATCH: Partial update of a book
    DELETE: Delete a book
    """
    # First, try to get the book
    try:
        book = Book.objects.get(pk=pk)
    except Book.DoesNotExist:
        return Response({
            'success': False,
            'message': 'Book not found'
        }, status=status.HTTP_404_NOT_FOUND)

    if request.method == 'GET':
        # Retrieve the book
        serializer = BookSerializer(book)
        return Response({
            'success': True,
            'data': serializer.data
        })

    elif request.method == 'PUT':
        # Full update - all fields required
        serializer = BookSerializer(book, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response({
                'success': True,
                'message': 'Book updated successfully',
                'data': serializer.data
            })
        return Response({
            'success': False,
            'message': 'Validation failed',
            'errors': serializer.errors
        }, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'PATCH':
        # Partial update - only provided fields
        serializer = BookSerializer(book, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response({
                'success': True,
                'message': 'Book partially updated',
                'data': serializer.data
            })
        return Response({
            'success': False,
            'message': 'Validation failed',
            'errors': serializer.errors
        }, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        # Delete the book
        book.delete()
        return Response({
            'success': True,
            'message': 'Book deleted successfully'
        }, status=status.HTTP_200_OK)
```

**URLs for Function-Based Views (books/urls.py):**

When using function-based views, you need to manually define each URL pattern instead of using a router:

```python
# books/urls.py (for Function-Based Views)

from django.urls import path
from . import views

urlpatterns = [
    path('', views.api_root, name='api-root'),
    path('books/', views.book_list, name='book-list'),
    path('books/<int:pk>/', views.book_detail, name='book-detail'),
]
```

**Comparison: Class-Based vs Function-Based Views**

| Aspect             | Class-Based Views (ViewSet) | Function-Based Views           |
| ------------------ | --------------------------- | ------------------------------ |
| **Code Length**    | Shorter, less repetitive    | Longer, more explicit          |
| **Learning Curve** | Steeper, more abstract      | Easier for beginners           |
| **Customization**  | Override specific methods   | Full control over logic        |
| **URL Routing**    | Automatic with Router       | Manual URL patterns            |
| **Readability**    | Organized by HTTP method    | All logic visible in one place |
| **Reusability**    | High (mixins, inheritance)  | Lower, more copy-paste         |
| **Best For**       | Standard CRUD operations    | Custom, non-standard APIs      |

Both approaches use the same serializer and produce identical API responses. Choose based on your preference and project requirements.

#### Step 6: Configure URLs (books/urls.py)

Create the URL routing for the API (using class-based ViewSet with Router):

```python
# books/urls.py

from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import BookViewSet, api_root

# Create a router and register our ViewSet
router = DefaultRouter()
router.register(r'books', BookViewSet, basename='book')

urlpatterns = [
    path('', api_root, name='api-root'),
    path('', include(router.urls)),
]
```

#### Step 7: Configure Project URLs (config/urls.py)

Include the books app URLs in the main project:

```python
# config/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('books.urls')),
]
```

#### Step 8: Run Migrations and Start Server

```bash
# Create database tables
python manage.py makemigrations
python manage.py migrate

# Create sample data (optional - run in Django shell)
python manage.py shell
```

In the Django shell, create some sample books:

```python
from books.models import Book

Book.objects.create(title="The Great Gatsby", author="F. Scott Fitzgerald", year=1925)
Book.objects.create(title="To Kill a Mockingbird", author="Harper Lee", year=1960)
Book.objects.create(title="1984", author="George Orwell", year=1949)

exit()
```

Start the development server:

```bash
python manage.py runserver
```

The server will start at `http://localhost:8000`

#### Testing the API

You can test the API using Thunder Client (VS Code extension), the DRF browsable API, or any API testing tool.

**Using Thunder Client (VS Code Extension):**

Thunder Client is a lightweight REST API client built into VS Code. Follow these steps to test the API:

**Step 1: Install Thunder Client**

1. Open VS Code
2. Go to Extensions (Ctrl+Shift+X)
3. Search for "Thunder Client"
4. Click Install

**Step 2: Open Thunder Client**

1. Click the Thunder Client icon in the VS Code sidebar (lightning bolt icon)
2. Click "New Request" button

**Step 3: GET All Books (List)**

1. Set the method dropdown to `GET`
2. Enter the URL: `http://localhost:8000/api/books/`
3. Click "Send"
4. You will see the JSON response with all books in the Response section

**Step 4: GET a Specific Book**

1. Click "New Request"
2. Set method to `GET`
3. Enter URL: `http://localhost:8000/api/books/1/`
4. Click "Send"
5. You will see the details of book with ID 1

**Step 5: Filter Books by Author**

1. Click "New Request"
2. Set method to `GET`
3. Enter URL: `http://localhost:8000/api/books/`
4. Click on the "Query" tab below the URL
5. Add a parameter: Key = `author`, Value = `orwell`
6. Click "Send"
7. You will see only books by authors matching "orwell"

**Step 6: POST - Create a New Book**

1. Click "New Request"
2. Set method to `POST`
3. Enter URL: `http://localhost:8000/api/books/`
4. Click on the "Body" tab
5. Select "JSON" from the dropdown
6. Enter the following JSON:

```json
{
  "title": "The Catcher in the Rye",
  "author": "J.D. Salinger",
  "year": 1951
}
```

7. Click "Send"
8. You will see a 201 Created response with the new book data

**Step 7: PUT - Update a Book (Full Replacement)**

1. Click "New Request"
2. Set method to `PUT`
3. Enter URL: `http://localhost:8000/api/books/1/`
4. Click on the "Body" tab
5. Select "JSON"
6. Enter the following JSON (all required fields):

```json
{
  "title": "The Great Gatsby - Revised Edition",
  "author": "F. Scott Fitzgerald",
  "year": 1926
}
```

7. Click "Send"
8. Book with ID 1 will be completely replaced with new data

**Step 8: PATCH - Partial Update**

1. Click "New Request"
2. Set method to `PATCH`
3. Enter URL: `http://localhost:8000/api/books/1/`
4. Click on the "Body" tab
5. Select "JSON"
6. Enter only the field you want to update:

```json
{
  "year": 2025
}
```

7. Click "Send"
8. Only the year field will be updated, other fields remain unchanged

**Step 9: DELETE - Remove a Book**

1. Click "New Request"
2. Set method to `DELETE`
3. Enter URL: `http://localhost:8000/api/books/2/`
4. Click "Send"
5. Book with ID 2 will be deleted
6. You will see a success message in the response

**Thunder Client Tips:**

- Save your requests to a Collection for reuse
- Use Environment variables for the base URL (e.g., `{{base_url}}/api/books/`)
- View response headers, status codes, and response time in the Response panel
- Use the "Tests" tab to add automated assertions

**Using the DRF Browsable API:**

Open `http://localhost:8000/api/books/` in your browser. DRF provides a user-friendly interface where you can:

- View the list of books
- Click on individual books to see details
- Use the built-in forms to create, update, or delete books
- See the raw JSON responses

This example demonstrates a complete RESTful API using Django REST Framework with proper HTTP verbs, meaningful endpoints, appropriate status codes, data validation through serializers, and consistent response formatting.

---
