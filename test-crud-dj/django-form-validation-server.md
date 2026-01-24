## Lab: Form Validation with Django

**Registration Form**

Build a Registration Form with comprehensive validation using Django.

**Prerequisites**

- Python 3.x installed
- pip (Python package installer)

---

**Create Project**

```bash
cd Desktop
mkdir django-form-validation
cd django-form-validation
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
python manage.py startapp registration
```

---

**Project Structure**

```text
django-form-validation/
├── config/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── registration/
│   ├── migrations/
│   ├── static/
│   │   └── registration/
│   │       └── css/
│   │           └── styles.css
│   ├── templates/
│   │   └── registration/
│   │       ├── form.html
│   │       └── success.html
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── forms.py
│   ├── models.py
│   ├── urls.py
│   └── views.py
├── media/
│   └── resumes/
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
    'registration',  # Add this line
]
```

**Add Media Settings** (at the bottom of `config/settings.py`)

```python
import os

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

---

**Create Model**

**Update `registration/models.py`**

```python
from django.db import models


class Registration(models.Model):
    GENDER_CHOICES = [
        ('M', 'Male'),
        ('F', 'Female'),
        ('O', 'Other'),
    ]

    HOBBY_CHOICES = [
        ('football', 'Football'),
        ('tableTennis', 'Table Tennis'),
        ('basketball', 'Basketball'),
    ]

    COUNTRY_CHOICES = [
        ('', '-- Select --'),
        ('Nepal', 'Nepal'),
        ('India', 'India'),
        ('USA', 'USA'),
    ]

    name = models.CharField(max_length=100)
    gender = models.CharField(max_length=1, choices=GENDER_CHOICES)
    hobbies = models.JSONField(default=list)  # Store multiple hobbies as JSON
    appointment = models.DateTimeField()
    country = models.CharField(max_length=50, choices=COUNTRY_CHOICES)
    email = models.EmailField(unique=True)
    phone = models.CharField(max_length=15)
    resume = models.FileField(upload_to='resumes/')
    password = models.CharField(max_length=128)
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

**Create Django Form with Validation**

**Create `registration/forms.py`**

```python
from django import forms
from django.core.validators import RegexValidator
from django.utils import timezone
from .models import Registration
import re


class RegistrationForm(forms.Form):
    """Registration form with comprehensive validation"""

    GENDER_CHOICES = [
        ('M', 'Male'),
        ('F', 'Female'),
        ('O', 'Other'),
    ]

    HOBBY_CHOICES = [
        ('football', 'Football'),
        ('tableTennis', 'Table Tennis'),
        ('basketball', 'Basketball'),
    ]

    COUNTRY_CHOICES = [
        ('', '-- Select --'),
        ('Nepal', 'Nepal'),
        ('India', 'India'),
        ('USA', 'USA'),
    ]

    # Name field
    name = forms.CharField(
        max_length=100,
        widget=forms.TextInput(attrs={
            'placeholder': 'Name',
            'id': 'name'
        }),
        error_messages={'required': 'Name is required'}
    )

    # Gender field (Radio buttons)
    gender = forms.ChoiceField(
        choices=GENDER_CHOICES,
        widget=forms.RadioSelect,
        error_messages={'required': 'Please select gender'}
    )

    # Hobbies field (Checkboxes)
    hobbies = forms.MultipleChoiceField(
        choices=HOBBY_CHOICES,
        widget=forms.CheckboxSelectMultiple,
        error_messages={'required': 'Please select at least one hobby'}
    )

    # Appointment field (DateTime)
    appointment = forms.DateTimeField(
        widget=forms.DateTimeInput(attrs={
            'type': 'datetime-local',
            'id': 'appointment'
        }),
        error_messages={'required': 'Please select appointment'}
    )

    # Country field (Select)
    country = forms.ChoiceField(
        choices=COUNTRY_CHOICES,
        error_messages={'required': 'Please select country'}
    )

    # Email field
    email = forms.EmailField(
        widget=forms.TextInput(attrs={
            'placeholder': 'Email',
            'id': 'email'
        }),
        error_messages={
            'required': 'Email is required',
            'invalid': 'Please enter a valid email'
        }
    )

    # Phone field
    phone = forms.CharField(
        max_length=15,
        widget=forms.NumberInput(attrs={
            'placeholder': 'Phone Number',
            'id': 'phone'
        }),
        error_messages={'required': 'Phone Number is required'}
    )

    # Resume field (File upload)
    resume = forms.FileField(
        widget=forms.FileInput(attrs={
            'accept': '.pdf,.jpg,.jpeg,.png,.doc,.docx',
            'id': 'resume'
        }),
        error_messages={'required': 'Please upload resume'}
    )

    # Password field
    password = forms.CharField(
        widget=forms.PasswordInput(attrs={
            'placeholder': 'Password',
            'id': 'password'
        }),
        error_messages={'required': 'Password is required'}
    )

    # Confirm Password field
    confirm_password = forms.CharField(
        widget=forms.PasswordInput(attrs={
            'placeholder': 'Confirm Password',
            'id': 'confirmPassword'
        }),
        error_messages={'required': 'Confirm Password is required'}
    )

    def clean_country(self):
        """Validate country selection"""
        country = self.cleaned_data.get('country')
        if not country:
            raise forms.ValidationError('Please select country')
        return country

    def clean_appointment(self):
        """Validate appointment is not in the past"""
        appointment = self.cleaned_data.get('appointment')
        if appointment:
            now = timezone.now()
            if appointment < now:
                raise forms.ValidationError(
                    'Appointment date & time cannot be in the past'
                )
        return appointment

    def clean_phone(self):
        """Validate phone number format (Nepal format)"""
        phone = self.cleaned_data.get('phone')
        if phone:
            # Nepal phone: starts with 9 and 10 digits OR starts with 01 and 8 digits
            phone_regex = r'^(?:9\d{9}|01\d{7})$'
            if not re.match(phone_regex, phone):
                raise forms.ValidationError(
                    'Please enter a valid phone number'
                )
        return phone

    def clean_resume(self):
        """Validate resume file type and size"""
        resume = self.cleaned_data.get('resume')
        if resume:
            # Check file extension
            allowed_extensions = ['pdf', 'jpg', 'jpeg', 'png', 'doc', 'docx']
            extension = resume.name.split('.')[-1].lower()
            if extension not in allowed_extensions:
                raise forms.ValidationError('Unsupported file format')

            # Check file size (max 2MB)
            max_size = 2 * 1024 * 1024  # 2MB in bytes
            if resume.size > max_size:
                raise forms.ValidationError(
                    'File size should be less than 2MB'
                )
        return resume

    def clean_password(self):
        """Validate password strength"""
        password = self.cleaned_data.get('password')
        if password:
            # At least 8 chars, 1 uppercase, 1 lowercase, 1 digit, 1 symbol
            password_regex = r'^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[^a-zA-Z\d\s]).{8,}$'
            if not re.match(password_regex, password):
                raise forms.ValidationError(
                    'Password must be at least 8 characters long and include '
                    'one uppercase letter, one lowercase letter, one number, '
                    'and one symbol'
                )
        return password

    def clean(self):
        """Validate confirm password matches password"""
        cleaned_data = super().clean()
        password = cleaned_data.get('password')
        confirm_password = cleaned_data.get('confirm_password')

        if password and confirm_password:
            if password != confirm_password:
                self.add_error(
                    'confirm_password',
                    'Confirm Password did not match Password'
                )

        return cleaned_data
```

---

**Create Views**

**Update `registration/views.py`**

```python
from django.shortcuts import render, redirect
from django.contrib import messages
from django.contrib.auth.hashers import make_password
from .forms import RegistrationForm
from .models import Registration


def registration_form(request):
    """Handle registration form display and submission"""
    if request.method == 'POST':
        form = RegistrationForm(request.POST, request.FILES)

        if form.is_valid():
            # Get cleaned data
            data = form.cleaned_data

            # Create registration instance
            registration = Registration(
                name=data['name'],
                gender=data['gender'],
                hobbies=data['hobbies'],
                appointment=data['appointment'],
                country=data['country'],
                email=data['email'],
                phone=data['phone'],
                resume=data['resume'],
                password=make_password(data['password']),  # Hash password
            )
            registration.save()

            messages.success(request, 'Form submitted successfully!')
            return redirect('registration:success')
    else:
        form = RegistrationForm()

    return render(request, 'registration/form.html', {'form': form})


def success_view(request):
    """Display success page after form submission"""
    return render(request, 'registration/success.html')
```

---

**Create App URLs**

**Create `registration/urls.py`**

```python
from django.urls import path
from . import views

app_name = 'registration'

urlpatterns = [
    path('', views.registration_form, name='form'),
    path('success/', views.success_view, name='success'),
]
```

---

**Configure Project URLs**

**Update `config/urls.py`**

```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('registration.urls')),
]

# Serve media files during development
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

**Setup Static Files**

**Create `registration/static/registration/css/styles.css`**

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: #f5f5f5;
  padding: 2rem;
}

.container {
  max-width: 500px;
  margin: 0 auto;
  background: #fff;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  margin-bottom: 1.5rem;
  color: #333;
}

.form-group {
  margin-bottom: 1rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: bold;
  color: #555;
}

.form-group input[type="text"],
.form-group input[type="password"],
.form-group input[type="number"],
.form-group input[type="datetime-local"],
.form-group select {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}

.form-group input:focus,
.form-group select:focus {
  outline: none;
  border-color: #06b6d4;
}

.radio-group,
.checkbox-group {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.radio-group label,
.checkbox-group label {
  display: flex;
  align-items: center;
  gap: 0.25rem;
  font-weight: normal;
}

.radio-group input,
.checkbox-group input {
  cursor: pointer;
}

.btn {
  width: 100%;
  padding: 0.75rem;
  background: #06b6d4;
  color: #fff;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  margin-top: 1rem;
}

.btn:hover {
  background: #0e7490;
}

.error {
  color: #dc2626;
  font-size: 0.875rem;
  margin-top: 0.25rem;
}

.error-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.alert {
  padding: 1rem;
  border-radius: 4px;
  margin-bottom: 1rem;
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

.success-container {
  text-align: center;
  padding: 3rem;
}

.success-container h1 {
  color: #065f46;
}

.success-container p {
  margin: 1rem 0;
  color: #666;
}

.success-container a {
  color: #06b6d4;
  text-decoration: none;
}

.success-container a:hover {
  text-decoration: underline;
}
```

---

**Create Templates**

**Create `registration/templates/registration/form.html`**

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Form Validation (Django)</title>
    <link rel="stylesheet" href="{% static 'registration/css/styles.css' %}" />
  </head>
  <body>
    <div class="container">
      <h1>Registration Form</h1>

      <!-- Display Messages -->
      {% if messages %} {% for message in messages %}
      <div class="alert alert-{{ message.tags }}">{{ message }}</div>
      {% endfor %} {% endif %}

      <form method="POST" enctype="multipart/form-data">
        {% csrf_token %}

        <!-- Name -->
        <div class="form-group">
          <label for="name">Name:</label>
          {{ form.name }} {% if form.name.errors %}
          <ul class="error-list">
            {% for error in form.name.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Gender -->
        <div class="form-group">
          <label>Gender:</label>
          <div class="radio-group">
            {% for radio in form.gender %}
            <label> {{ radio.tag }} {{ radio.choice_label }} </label>
            {% endfor %}
          </div>
          {% if form.gender.errors %}
          <ul class="error-list">
            {% for error in form.gender.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Hobbies -->
        <div class="form-group">
          <label>Hobbies:</label>
          <div class="checkbox-group">
            {% for checkbox in form.hobbies %}
            <label> {{ checkbox.tag }} {{ checkbox.choice_label }} </label>
            {% endfor %}
          </div>
          {% if form.hobbies.errors %}
          <ul class="error-list">
            {% for error in form.hobbies.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Appointment -->
        <div class="form-group">
          <label for="appointment">Appointment Date & Time:</label>
          {{ form.appointment }} {% if form.appointment.errors %}
          <ul class="error-list">
            {% for error in form.appointment.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Country -->
        <div class="form-group">
          <label for="country">Country:</label>
          {{ form.country }} {% if form.country.errors %}
          <ul class="error-list">
            {% for error in form.country.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Email -->
        <div class="form-group">
          <label for="email">Email:</label>
          {{ form.email }} {% if form.email.errors %}
          <ul class="error-list">
            {% for error in form.email.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Phone -->
        <div class="form-group">
          <label for="phone">Phone:</label>
          {{ form.phone }} {% if form.phone.errors %}
          <ul class="error-list">
            {% for error in form.phone.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Resume -->
        <div class="form-group">
          <label for="resume">Upload Resume (PDF / Image / Word):</label>
          {{ form.resume }} {% if form.resume.errors %}
          <ul class="error-list">
            {% for error in form.resume.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Password -->
        <div class="form-group">
          <label for="password">Password:</label>
          {{ form.password }} {% if form.password.errors %}
          <ul class="error-list">
            {% for error in form.password.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <!-- Confirm Password -->
        <div class="form-group">
          <label for="confirmPassword">Confirm Password:</label>
          {{ form.confirm_password }} {% if form.confirm_password.errors %}
          <ul class="error-list">
            {% for error in form.confirm_password.errors %}
            <li class="error">{{ error }}</li>
            {% endfor %}
          </ul>
          {% endif %}
        </div>

        <button type="submit" class="btn">Submit</button>
      </form>
    </div>
  </body>
</html>
```

**Create `registration/templates/registration/success.html`**

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Success - Django Form</title>
    <link rel="stylesheet" href="{% static 'registration/css/styles.css' %}" />
  </head>
  <body>
    <div class="container success-container">
      <h1>✓ Success!</h1>
      <p>Your form has been submitted successfully.</p>
      <a href="{% url 'registration:form' %}">← Back to Form</a>
    </div>
  </body>
</html>
```

---

**Register Model in Admin**

**Update `registration/admin.py`**

```python
from django.contrib import admin
from .models import Registration


@admin.register(Registration)
class RegistrationAdmin(admin.ModelAdmin):
    list_display = ['name', 'email', 'gender', 'country', 'created_at']
    list_filter = ['gender', 'country', 'created_at']
    search_fields = ['name', 'email', 'phone']
    readonly_fields = ['password', 'created_at']
```

---

**Run the Project**

```bash
python manage.py runserver
```

Open your browser and navigate to `http://127.0.0.1:8000/` to see your Registration Form in action.

---

**Validation Comparison**

| Validation               | JavaScript Version                       | Django Version                        |
| ------------------------ | ---------------------------------------- | ------------------------------------- |
| **Name Required**        | `if (!name) return alert(...)`           | `required` attribute + `clean_name()` |
| **Gender Required**      | `if (!gender) return alert(...)`         | `forms.ChoiceField` with required     |
| **Hobbies Required**     | `if (!hobbies.length) return alert(...)` | `forms.MultipleChoiceField` required  |
| **Appointment Not Past** | `if (selectedDate < now)`                | `clean_appointment()` method          |
| **Country Required**     | `if (!country) return alert(...)`        | `clean_country()` method              |
| **Email Format**         | `emailRegex.test(email)`                 | `forms.EmailField` built-in           |
| **Phone Format**         | `phoneRegex.test(phone)`                 | `clean_phone()` with regex            |
| **File Type**            | `allowedExtensions.includes()`           | `clean_resume()` method               |
| **File Size**            | `resume.size > maxSize`                  | `clean_resume()` method               |
| **Password Strength**    | `passwordRegex.test(password)`           | `clean_password()` with regex         |
| **Password Match**       | `confirmPassword !== password`           | `clean()` method                      |

---

**Key Differences from JavaScript Version**

1. **Server-side Validation**: Django validates on server, more secure than client-side
2. **Form Class**: Uses Django's Form class with `clean_*` methods
3. **CSRF Protection**: Built-in protection against cross-site request forgery
4. **File Handling**: Django handles file uploads and storage automatically
5. **Password Hashing**: Uses `make_password()` for secure password storage
6. **Database Storage**: Data persists in database instead of just console.log
7. **Error Display**: Errors shown inline with form fields

---

**Django Form Validation Methods**

| Method                | Purpose                                       |
| --------------------- | --------------------------------------------- |
| `clean_<fieldname>()` | Validate individual field                     |
| `clean()`             | Cross-field validation (e.g., password match) |
| `is_valid()`          | Run all validations                           |
| `cleaned_data`        | Access validated data                         |
| `errors`              | Access validation errors                      |

---

**Output**

The form will display validation errors inline for each field when validation fails. On successful submission, the user is redirected to a success page.

---

---

---
