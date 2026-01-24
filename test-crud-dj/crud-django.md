## Lab: CRUD with Django

**Grocery Bud**

Build a Grocery list app using Django.

**Prerequisites**

- Python 3.x installed
- pip (Python package installer)

---

**Create Project**

```bash
cd Desktop
mkdir grocery-bud-django
cd grocery-bud-django
```

**Create Virtual Environment**

```bash
python -m venv venv
```

**Activate Virtual Environment**

```bash
# Windows
venv\Scripts\activate

# Linux/Mac
source venv/bin/activate
```

**Install Django**

```bash
pip install django
```

**Create Django Project**

```bash
django-admin startproject config .
```

**Create Django App**

```bash
python manage.py startapp grocery
```

---

**Project Structure**

```text
grocery-bud-django/
├── config/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── grocery/
│   ├── migrations/
│   ├── static/
│   │   └── grocery/
│   │       └── css/
│   │           ├── global.css
│   │           ├── single-item.css
│   │           ├── items.css
│   │           └── form.css
│   ├── templates/
│   │   └── grocery/
│   │       └── index.html
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── urls.py
│   └── views.py
├── manage.py
└── venv/
```

---

**Register App in Settings**

**Update `config/settings.py`**

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'grocery',  # Add this line
]
```

---

**Create Model**

**Update `grocery/models.py`**

```python
from django.db import models


class GroceryItem(models.Model):
    name = models.CharField(max_length=200)
    completed = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name

    class Meta:
        ordering = ['-created_at']
```

---

**Create and Run Migrations**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

**Setup Static Files**

**Create `grocery/static/grocery/css/global.css`**

```css
*,
::after,
::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: #eee;
  font-family: Helvetica, sans-serif;
  font-weight: 400;
  line-height: 1.2;
  color: #222;
}

.section-center {
  margin: 0 auto;
  margin-top: 8rem;
  max-width: 30rem;
  background: #fff;
  border-radius: 0.25rem;
  padding: 2rem;
  position: relative;
}

.alert {
  padding: 0.75rem 1rem;
  margin-bottom: 1rem;
  border-radius: 0.25rem;
  text-align: center;
}

.alert-success {
  background: #d1fae5;
  color: #065f46;
}

.alert-error {
  background: #fee2e2;
  color: #991b1b;
}
```

**Create `grocery/static/grocery/css/single-item.css`**

```css
.single-item {
  display: grid;
  grid-template-columns: auto 1fr auto auto;
  column-gap: 1rem;
  align-items: center;
}

.single-item p {
  text-transform: capitalize;
}

.single-item input[type="checkbox"] {
  cursor: pointer;
  width: 1rem;
  height: 1rem;
}

.single-item .btn {
  cursor: pointer;
  color: #fff;
  background: #06b6d4;
  border: transparent;
  border-radius: 0.25rem;
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  text-decoration: none;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

.single-item .btn:hover {
  background: #0e7490;
}

.single-item .remove-btn {
  background: #222;
}

.single-item .remove-btn:hover {
  background: #900e0e;
}

.completed-text {
  text-decoration: line-through;
}
```

**Create `grocery/static/grocery/css/items.css`**

```css
.items {
  margin-top: 2rem;
  display: grid;
  row-gap: 1rem;
}
```

**Create `grocery/static/grocery/css/form.css`**

```css
form h2 {
  text-align: center;
  margin-bottom: 1.5rem;
  text-transform: capitalize;
  font-weight: normal;
}

.form-control {
  display: grid;
  grid-template-columns: 1fr 100px;
}

.form-input {
  width: 100%;
  padding: 0.375rem 0.75rem;
  border-radius: 0;
  border-top-left-radius: 0.25rem;
  border-bottom-left-radius: 0.25rem;
  border: 1px solid #ddd;
}

.form-input::placeholder {
  color: #aaa;
}

.form-control .btn {
  cursor: pointer;
  color: #fff;
  background: #06b6d4;
  border: transparent;
  border-radius: 0;
  border-top-right-radius: 0.25rem;
  border-bottom-right-radius: 0.25rem;
  padding: 0.375rem 0.75rem;
  text-transform: capitalize;
}

.form-control .btn:hover {
  background: #0e7490;
}
```

---

**Create Template**

**Create `grocery/templates/grocery/index.html`**

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Grocery Bud - Django</title>
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css"
    />
    <link rel="stylesheet" href="{% static 'grocery/css/global.css' %}" />
    <link rel="stylesheet" href="{% static 'grocery/css/single-item.css' %}" />
    <link rel="stylesheet" href="{% static 'grocery/css/items.css' %}" />
    <link rel="stylesheet" href="{% static 'grocery/css/form.css' %}" />
  </head>
  <body>
    <section class="section-center">
      <!-- Messages -->
      {% if messages %} {% for message in messages %}
      <div class="alert alert-{{ message.tags }}">{{ message }}</div>
      {% endfor %} {% endif %}

      <!-- Form -->
      <form
        method="POST"
        action="{% if edit_item %}{% url 'grocery:update' edit_item.id %}{% else %}{% url 'grocery:add' %}{% endif %}"
      >
        {% csrf_token %}
        <h2>grocery bud</h2>
        <div class="form-control">
          <input
            type="text"
            name="name"
            class="form-input"
            placeholder="e.g. eggs"
            value="{{ edit_item.name|default:'' }}"
            autofocus
          />
          <button type="submit" class="btn">
            {% if edit_item %}edit item{% else %}add item{% endif %}
          </button>
        </div>
      </form>

      <!-- Items List -->
      <div class="items">
        {% for item in items %}
        <div class="single-item">
          <form method="POST" action="{% url 'grocery:toggle' item.id %}">
            {% csrf_token %}
            <input
              type="checkbox"
              {%
              if
              item.completed
              %}checked{%
              endif
              %}
              onchange="this.form.submit()"
            />
          </form>
          <p class="{% if item.completed %}completed-text{% endif %}">
            {{ item.name }}
          </p>
          <a
            href="{% url 'grocery:edit' item.id %}"
            class="btn icon-btn edit-btn"
          >
            <i class="fa-regular fa-pen-to-square"></i>
          </a>
          <form
            method="POST"
            action="{% url 'grocery:delete' item.id %}"
            style="display: inline;"
          >
            {% csrf_token %}
            <button type="submit" class="btn icon-btn remove-btn">
              <i class="fa-regular fa-trash-can"></i>
            </button>
          </form>
        </div>
        {% empty %}
        <p style="text-align: center; color: #888;">
          No items yet. Add one above!
        </p>
        {% endfor %}
      </div>
    </section>
  </body>
</html>
```

---

**Create Views**

**Update `grocery/views.py`**

```python
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib import messages
from .models import GroceryItem


def index(request):
    """Display all grocery items and handle edit mode"""
    items = GroceryItem.objects.all()
    edit_id = request.GET.get('edit')
    edit_item = None

    if edit_id:
        edit_item = get_object_or_404(GroceryItem, id=edit_id)

    context = {
        'items': items,
        'edit_item': edit_item,
    }
    return render(request, 'grocery/index.html', context)


def add_item(request):
    """Add a new grocery item"""
    if request.method == 'POST':
        name = request.POST.get('name', '').strip()

        if not name:
            messages.error(request, 'Please provide a value')
            return redirect('grocery:index')

        GroceryItem.objects.create(name=name)
        messages.success(request, 'Item Added Successfully!')

    return redirect('grocery:index')


def edit_item(request, item_id):
    """Redirect to index with edit parameter"""
    return redirect(f"/?edit={item_id}")


def update_item(request, item_id):
    """Update an existing grocery item name"""
    if request.method == 'POST':
        item = get_object_or_404(GroceryItem, id=item_id)
        name = request.POST.get('name', '').strip()

        if not name:
            messages.error(request, 'Please provide a value')
            return redirect('grocery:index')

        item.name = name
        item.save()
        messages.success(request, 'Item Updated Successfully!')

    return redirect('grocery:index')


def delete_item(request, item_id):
    """Delete a grocery item"""
    if request.method == 'POST':
        item = get_object_or_404(GroceryItem, id=item_id)
        item.delete()
        messages.success(request, 'Item Deleted Successfully!')

    return redirect('grocery:index')


def toggle_completed(request, item_id):
    """Toggle the completed status of a grocery item"""
    if request.method == 'POST':
        item = get_object_or_404(GroceryItem, id=item_id)
        item.completed = not item.completed
        item.save()

    return redirect('grocery:index')
```

---

**Create App URLs**

**Create `grocery/urls.py`**

```python
from django.urls import path
from . import views

app_name = 'grocery'

urlpatterns = [
    path('', views.index, name='index'),
    path('add/', views.add_item, name='add'),
    path('edit/<int:item_id>/', views.edit_item, name='edit'),
    path('update/<int:item_id>/', views.update_item, name='update'),
    path('delete/<int:item_id>/', views.delete_item, name='delete'),
    path('toggle/<int:item_id>/', views.toggle_completed, name='toggle'),
]
```

---

**Configure Project URLs**

**Update `config/urls.py`**

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('grocery.urls')),
]
```

---

**Configure Messages Framework**

**Update `config/settings.py`** (Add at the bottom)

```python
from django.contrib.messages import constants as messages

MESSAGE_TAGS = {
    messages.ERROR: 'error',
    messages.SUCCESS: 'success',
}
```

---

**Register Model in Admin**

**Update `grocery/admin.py`**

```python
from django.contrib import admin
from .models import GroceryItem


@admin.register(GroceryItem)
class GroceryItemAdmin(admin.ModelAdmin):
    list_display = ['name', 'completed', 'created_at']
    list_filter = ['completed']
    search_fields = ['name']
```

---

**Create Superuser (Optional)**

```bash
python manage.py createsuperuser
```

---

**Run the Project**

```bash
python manage.py runserver
```

Open your browser and navigate to `http://127.0.0.1:8000/` to see your Grocery Bud in action.

---

**Features Summary**

| Feature              | JavaScript Version                     | Django Version                |
| -------------------- | -------------------------------------- | ----------------------------- |
| **Create**           | `addItem()` function with localStorage | `add_item` view with database |
| **Read**             | `render()` from localStorage           | `index` view with QuerySet    |
| **Update**           | `updateItemName()` function            | `update_item` view            |
| **Delete**           | `removeItem()` function                | `delete_item` view            |
| **Toggle Completed** | `editCompleted()` function             | `toggle_completed` view       |
| **Data Storage**     | localStorage                           | SQLite Database               |
| **Notifications**    | `alert()`                              | Django Messages Framework     |

---

**Key Differences from JavaScript Version**

1. **Backend Processing**: Django handles all CRUD operations on the server-side
2. **Database Storage**: Data persists in SQLite database instead of localStorage
3. **Form Handling**: Uses Django's CSRF protection and form processing
4. **URL Routing**: Django's URL dispatcher handles different operations
5. **Template System**: Django template language replaces JavaScript DOM manipulation
6. **Messages**: Django's messages framework replaces JavaScript alerts

---

**Output**

At each stage, you should see a list of grocery items displayed with proper spacing, similar to the JavaScript version.

The functionality includes:

- Add new grocery items
- Edit existing item names
- Mark items as completed/uncompleted
- Delete items
- Persistent storage in database

---

---

---
