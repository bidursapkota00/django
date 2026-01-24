# Django Questions & Solutions

---

## Q1: Student Login View

**Question:** Write a view that accepts username and password as arguments and check with student table, if credential match, redirect to dashboard page otherwise display 'Invalid username/password'.

---

### Solution

**Step 1: Create Student Model**

```python
# models.py
from django.db import models


class Student(models.Model):
    username = models.CharField(max_length=100, unique=True)
    password = models.CharField(max_length=100)
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.username
```

---

**Step 2: Run Migrations**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

**Step 3: Create Login View**

```python
# views.py
from django.shortcuts import render, redirect


def student_login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')

        try:
            student = Student.objects.get(username=username, password=password)
            # Credentials match - redirect to dashboard
            return redirect('dashboard')
        except Student.DoesNotExist:
            # Invalid credentials
            return render(request, 'login.html', {
                'error': 'Invalid username/password'
            })

    return render(request, 'login.html')


def dashboard(request):
    return render(request, 'dashboard.html')
```

---

**Step 4: Create Login Template**

```html
<!-- templates/login.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Student Login</title>
  </head>
  <body>
    <h1>Student Login</h1>

    {% if error %}
    <p style="color: red;">{{ error }}</p>
    {% endif %}

    <form method="POST">
      {% csrf_token %}
      <label>Username:</label>
      <input type="text" name="username" required /><br /><br />

      <label>Password:</label>
      <input type="password" name="password" required /><br /><br />

      <button type="submit">Login</button>
    </form>
  </body>
</html>
```

---

**Step 5: Create Dashboard Template**

```html
<!-- templates/dashboard.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Dashboard</title>
  </head>
  <body>
    <h1>Welcome to Dashboard</h1>
    <p>You have successfully logged in.</p>
  </body>
</html>
```

---

**Step 6: Configure URLs**

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('login/', views.student_login, name='login'),
    path('dashboard/', views.dashboard, name='dashboard'),
]
```

---

### Alternative Solution (Using Session)

```python
# views.py
from django.shortcuts import render, redirect
from .models import Student


def student_login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')

        try:
            student = Student.objects.get(username=username, password=password)
            # Store student in session
            request.session['student_id'] = student.id
            request.session['student_name'] = student.name
            return redirect('dashboard')
        except Student.DoesNotExist:
            return render(request, 'login.html', {
                'error': 'Invalid username/password'
            })

    return render(request, 'login.html')


def dashboard(request):
    # Check if student is logged in
    if 'student_id' not in request.session:
        return redirect('login')

    student_name = request.session.get('student_name')
    return render(request, 'dashboard.html', {'name': student_name})


def logout(request):
    # Clear session
    request.session.flush()
    return redirect('login')
```

---

### Solution with Hashed Password (Recommended)

```python
# views.py
from django.shortcuts import render, redirect
from django.contrib.auth.hashers import check_password
from .models import Student


def student_login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')

        try:
            student = Student.objects.get(username=username)
            # Check hashed password
            if check_password(password, student.password):
                request.session['student_id'] = student.id
                return redirect('dashboard')
            else:
                return render(request, 'login.html', {
                    'error': 'Invalid username/password'
                })
        except Student.DoesNotExist:
            return render(request, 'login.html', {
                'error': 'Invalid username/password'
            })

    return render(request, 'login.html')
```

---

### Summary

| Component    | Purpose                       |
| ------------ | ----------------------------- |
| **Model**    | Stores student credentials    |
| **View**     | Handles login logic           |
| **Template** | Displays login form and error |
| **URL**      | Routes to view                |

---

---

## Q2: Patient Registration Form with Server-Side Validation

**Question:** Write server side script to create and validate form with following rule and store given data into 'patients' table with details (name, patient_id, mobile, gender, address, dob, doctor name):

- Name, Mobile, Doctor Name, Gender, DOB: Required
- Mobile: 10 digit start with 98, 97 or 96
- DOB: YYYY-MM-DD format

---

### Solution

**Step 1: Create Patient Model**

```python
# models.py
from django.db import models


class Patient(models.Model):
    GENDER_CHOICES = [
        ('M', 'Male'),
        ('F', 'Female'),
        ('O', 'Other'),
    ]

    name = models.CharField(max_length=200)
    patient_id = models.CharField(max_length=50, unique=True)
    mobile = models.CharField(max_length=10)
    gender = models.CharField(max_length=1, choices=GENDER_CHOICES)
    address = models.TextField(blank=True, null=True)
    dob = models.DateField()
    doctor_name = models.CharField(max_length=200)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.name} ({self.patient_id})"
```

---

**Step 2: Run Migrations**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

**Step 3: Create Django Form with Validation**

```python
# forms.py
from django import forms
from .models import Patient
import re


class PatientForm(forms.Form):
    GENDER_CHOICES = [
        ('', '-- Select Gender --'),
        ('M', 'Male'),
        ('F', 'Female'),
        ('O', 'Other'),
    ]

    name = forms.CharField(
        max_length=200,
        error_messages={'required': 'Name is required'}
    )

    patient_id = forms.CharField(
        max_length=50,
        required=False
    )

    mobile = forms.CharField(
        max_length=10,
        error_messages={'required': 'Mobile is required'}
    )

    gender = forms.ChoiceField(
        choices=GENDER_CHOICES,
        error_messages={'required': 'Gender is required'}
    )

    address = forms.CharField(
        widget=forms.Textarea,
        required=False
    )

    dob = forms.DateField(
        widget=forms.DateInput(attrs={'type': 'date'}),
        error_messages={'required': 'Date of Birth is required'}
    )

    doctor_name = forms.CharField(
        max_length=200,
        error_messages={'required': 'Doctor Name is required'}
    )

    def clean_mobile(self):
        """Validate mobile: 10 digits starting with 98, 97 or 96"""
        mobile = self.cleaned_data.get('mobile')

        if not mobile:
            raise forms.ValidationError('Mobile is required')

        # Check if 10 digits and starts with 98, 97 or 96
        mobile_regex = r'^(98|97|96)\d{8}$'
        if not re.match(mobile_regex, mobile):
            raise forms.ValidationError(
                'Mobile must be 10 digits and start with 98, 97 or 96'
            )

        return mobile

    def clean_gender(self):
        """Validate gender selection"""
        gender = self.cleaned_data.get('gender')
        if not gender:
            raise forms.ValidationError('Gender is required')
        return gender

    def clean_dob(self):
        """Validate DOB format (YYYY-MM-DD)"""
        dob = self.cleaned_data.get('dob')
        if not dob:
            raise forms.ValidationError('Date of Birth is required')
        # Django's DateField automatically validates YYYY-MM-DD format
        return dob
```

---

**Step 4: Create View**

```python
# views.py
from django.shortcuts import render, redirect
from django.contrib import messages
from .forms import PatientForm
from .models import Patient
import uuid


def patient_registration(request):
    if request.method == 'POST':
        form = PatientForm(request.POST)

        if form.is_valid():
            # Generate patient_id if not provided
            patient_id = form.cleaned_data.get('patient_id')
            if not patient_id:
                patient_id = 'PAT-' + str(uuid.uuid4())[:8].upper()

            # Create patient record
            patient = Patient(
                name=form.cleaned_data['name'],
                patient_id=patient_id,
                mobile=form.cleaned_data['mobile'],
                gender=form.cleaned_data['gender'],
                address=form.cleaned_data.get('address', ''),
                dob=form.cleaned_data['dob'],
                doctor_name=form.cleaned_data['doctor_name'],
            )
            patient.save()

            messages.success(request, 'Patient registered successfully!')
            return redirect('patient_list')
    else:
        form = PatientForm()

    return render(request, 'patient_form.html', {'form': form})


def patient_list(request):
    patients = Patient.objects.all()
    return render(request, 'patient_list.html', {'patients': patients})
```

---

**Step 5: Create Patient Form Template**

```html
<!-- templates/patient_form.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Patient Registration</title>
    <style>
      .error {
        color: red;
        font-size: 0.9em;
      }
      .form-group {
        margin-bottom: 15px;
      }
      label {
        display: block;
        margin-bottom: 5px;
      }
      input,
      select,
      textarea {
        padding: 8px;
        width: 300px;
      }
    </style>
  </head>
  <body>
    <h1>Patient Registration Form</h1>

    {% if messages %} {% for message in messages %}
    <p style="color: green;">{{ message }}</p>
    {% endfor %} {% endif %}

    <form method="POST">
      {% csrf_token %}

      <div class="form-group">
        <label>Name: *</label>
        <input
          type="text"
          name="name"
          value="{{ form.name.value|default:'' }}"
        />
        {% if form.name.errors %}
        <p class="error">{{ form.name.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Patient ID:</label>
        <input
          type="text"
          name="patient_id"
          value="{{ form.patient_id.value|default:'' }}"
          placeholder="Auto-generated if empty"
        />
      </div>

      <div class="form-group">
        <label>Mobile: * (10 digits, start with 98/97/96)</label>
        <input
          type="text"
          name="mobile"
          value="{{ form.mobile.value|default:'' }}"
        />
        {% if form.mobile.errors %}
        <p class="error">{{ form.mobile.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Gender: *</label>
        <select name="gender">
          <option value="">-- Select Gender --</option>
          <option
            value="M"
            {%
            if
            form.gender.value=""
            ="M"
            %}selected{%
            endif
            %}
          >
            Male
          </option>
          <option
            value="F"
            {%
            if
            form.gender.value=""
            ="F"
            %}selected{%
            endif
            %}
          >
            Female
          </option>
          <option
            value="O"
            {%
            if
            form.gender.value=""
            ="O"
            %}selected{%
            endif
            %}
          >
            Other
          </option>
        </select>
        {% if form.gender.errors %}
        <p class="error">{{ form.gender.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Address:</label>
        <textarea name="address">{{ form.address.value|default:'' }}</textarea>
      </div>

      <div class="form-group">
        <label>Date of Birth: * (YYYY-MM-DD)</label>
        <input type="date" name="dob" value="{{ form.dob.value|default:'' }}" />
        {% if form.dob.errors %}
        <p class="error">{{ form.dob.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Doctor Name: *</label>
        <input
          type="text"
          name="doctor_name"
          value="{{ form.doctor_name.value|default:'' }}"
        />
        {% if form.doctor_name.errors %}
        <p class="error">{{ form.doctor_name.errors.0 }}</p>
        {% endif %}
      </div>

      <button type="submit">Register Patient</button>
    </form>
  </body>
</html>
```

---

**Step 6: Create Patient List Template**

```html
<!-- templates/patient_list.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Patient List</title>
  </head>
  <body>
    <h1>Registered Patients</h1>
    <a href="{% url 'patient_register' %}">+ Add New Patient</a>

    <table border="1" cellpadding="10" style="margin-top: 20px;">
      <tr>
        <th>Patient ID</th>
        <th>Name</th>
        <th>Mobile</th>
        <th>Gender</th>
        <th>DOB</th>
        <th>Doctor</th>
      </tr>
      {% for patient in patients %}
      <tr>
        <td>{{ patient.patient_id }}</td>
        <td>{{ patient.name }}</td>
        <td>{{ patient.mobile }}</td>
        <td>{{ patient.get_gender_display }}</td>
        <td>{{ patient.dob }}</td>
        <td>{{ patient.doctor_name }}</td>
      </tr>
      {% empty %}
      <tr>
        <td colspan="6">No patients registered yet.</td>
      </tr>
      {% endfor %}
    </table>
  </body>
</html>
```

---

**Step 7: Configure URLs**

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('patient/register/', views.patient_registration, name='patient_register'),
    path('patients/', views.patient_list, name='patient_list'),
]
```

---

### Validation Rules Summary

| Field           | Validation Rule                                   |
| --------------- | ------------------------------------------------- |
| **Name**        | Required                                          |
| **Mobile**      | Required, 10 digits, must start with 98, 97 or 96 |
| **Gender**      | Required (M/F/O)                                  |
| **DOB**         | Required, YYYY-MM-DD format                       |
| **Doctor Name** | Required                                          |
| **Patient ID**  | Optional (auto-generated if empty)                |
| **Address**     | Optional                                          |

---

### Mobile Validation Regex Explained

```python
mobile_regex = r'^(98|97|96)\d{8}$'
```

| Part           | Meaning                      |
| -------------- | ---------------------------- |
| `^`            | Start of string              |
| `(98\|97\|96)` | Must start with 98, 97 or 96 |
| `\d{8}`        | Followed by exactly 8 digits |
| `$`            | End of string                |

**Valid Examples:** 9841234567, 9712345678, 9698765432

**Invalid Examples:** 9012345678, 984123456, 98412345678

---

---

## Q3: User Registration Form with Validation

**Question:** Design following forms in HTML and write corresponding server side code to store the user's values after satisfying following validation rules:

- Length of Full name up to 40 characters
- Email address must be valid email address
- Username must start with string and followed by number
- Password length must be more than 8 characters

---

### Solution

**Step 1: Create User Model**

```python
# models.py
from django.db import models


class User(models.Model):
    full_name = models.CharField(max_length=40)
    email = models.EmailField(unique=True)
    username = models.CharField(max_length=100, unique=True)
    password = models.CharField(max_length=128)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.username
```

---

**Step 2: Run Migrations**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

**Step 3: Create Django Form with Validation**

```python
# forms.py
from django import forms
import re


class UserRegistrationForm(forms.Form):
    full_name = forms.CharField(
        max_length=40,
        error_messages={
            'required': 'Full name is required',
            'max_length': 'Full name must be up to 40 characters'
        }
    )

    email = forms.EmailField(
        error_messages={
            'required': 'Email is required',
            'invalid': 'Please enter a valid email address'
        }
    )

    username = forms.CharField(
        max_length=100,
        error_messages={'required': 'Username is required'}
    )

    password = forms.CharField(
        widget=forms.PasswordInput,
        error_messages={'required': 'Password is required'}
    )

    def clean_full_name(self):
        """Validate full name length up to 40 characters"""
        full_name = self.cleaned_data.get('full_name')

        if not full_name:
            raise forms.ValidationError('Full name is required')

        if len(full_name) > 40:
            raise forms.ValidationError(
                'Full name must be up to 40 characters'
            )

        return full_name

    def clean_username(self):
        """Validate username: must start with string and followed by number"""
        username = self.cleaned_data.get('username')

        if not username:
            raise forms.ValidationError('Username is required')

        # Pattern: starts with letters, followed by at least one number
        username_regex = r'^[a-zA-Z]+\d+$'
        if not re.match(username_regex, username):
            raise forms.ValidationError(
                'Username must start with letters and end with numbers'
            )

        return username

    def clean_password(self):
        """Validate password length more than 8 characters"""
        password = self.cleaned_data.get('password')

        if not password:
            raise forms.ValidationError('Password is required')

        if len(password) <= 8:
            raise forms.ValidationError(
                'Password must be more than 8 characters'
            )

        return password
```

---

**Step 4: Create View**

```python
# views.py
from django.shortcuts import render, redirect
from django.contrib import messages
from django.contrib.auth.hashers import make_password
from .forms import UserRegistrationForm
from .models import User


def user_registration(request):
    if request.method == 'POST':
        form = UserRegistrationForm(request.POST)

        if form.is_valid():
            # Check if email already exists
            email = form.cleaned_data['email']
            if User.objects.filter(email=email).exists():
                form.add_error('email', 'Email already registered')
                return render(request, 'user_form.html', {'form': form})

            # Check if username already exists
            username = form.cleaned_data['username']
            if User.objects.filter(username=username).exists():
                form.add_error('username', 'Username already taken')
                return render(request, 'user_form.html', {'form': form})

            # Create user record
            user = User(
                full_name=form.cleaned_data['full_name'],
                email=email,
                username=username,
                password=make_password(form.cleaned_data['password']),
            )
            user.save()

            messages.success(request, 'User registered successfully!')
            return redirect('user_list')
    else:
        form = UserRegistrationForm()

    return render(request, 'user_form.html', {'form': form})


def user_list(request):
    users = User.objects.all()
    return render(request, 'user_list.html', {'users': users})
```

---

**Step 5: Create Registration Form Template (HTML)**

```html
<!-- templates/user_form.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>User Registration</title>
    <style>
      .error {
        color: red;
        font-size: 0.9em;
      }
      .form-group {
        margin-bottom: 15px;
      }
      label {
        display: block;
        margin-bottom: 5px;
      }
      input {
        padding: 8px;
        width: 300px;
      }
      .hint {
        color: gray;
        font-size: 0.8em;
      }
    </style>
  </head>
  <body>
    <h1>User Registration Form</h1>

    {% if messages %} {% for message in messages %}
    <p style="color: green;">{{ message }}</p>
    {% endfor %} {% endif %}

    <form method="POST">
      {% csrf_token %}

      <div class="form-group">
        <label>Full Name: *</label>
        <input
          type="text"
          name="full_name"
          maxlength="40"
          value="{{ form.full_name.value|default:'' }}"
        />
        <p class="hint">Maximum 40 characters</p>
        {% if form.full_name.errors %}
        <p class="error">{{ form.full_name.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Email: *</label>
        <input
          type="email"
          name="email"
          value="{{ form.email.value|default:'' }}"
        />
        {% if form.email.errors %}
        <p class="error">{{ form.email.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Username: *</label>
        <input
          type="text"
          name="username"
          value="{{ form.username.value|default:'' }}"
        />
        <p class="hint">
          Must start with letters and end with numbers (e.g., john123)
        </p>
        {% if form.username.errors %}
        <p class="error">{{ form.username.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Password: *</label>
        <input type="password" name="password" />
        <p class="hint">Must be more than 8 characters</p>
        {% if form.password.errors %}
        <p class="error">{{ form.password.errors.0 }}</p>
        {% endif %}
      </div>

      <button type="submit">Register</button>
    </form>
  </body>
</html>
```

---

**Step 6: Create User List Template**

```html
<!-- templates/user_list.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>User List</title>
  </head>
  <body>
    <h1>Registered Users</h1>
    <a href="{% url 'user_register' %}">+ Add New User</a>

    <table border="1" cellpadding="10" style="margin-top: 20px;">
      <tr>
        <th>ID</th>
        <th>Full Name</th>
        <th>Email</th>
        <th>Username</th>
        <th>Created At</th>
      </tr>
      {% for user in users %}
      <tr>
        <td>{{ user.id }}</td>
        <td>{{ user.full_name }}</td>
        <td>{{ user.email }}</td>
        <td>{{ user.username }}</td>
        <td>{{ user.created_at }}</td>
      </tr>
      {% empty %}
      <tr>
        <td colspan="5">No users registered yet.</td>
      </tr>
      {% endfor %}
    </table>
  </body>
</html>
```

---

**Step 7: Configure URLs**

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('register/', views.user_registration, name='user_register'),
    path('users/', views.user_list, name='user_list'),
]
```

---

### Validation Rules Summary

| Field         | Validation Rule                      | Regex/Method           |
| ------------- | ------------------------------------ | ---------------------- |
| **Full Name** | Up to 40 characters                  | `len(full_name) <= 40` |
| **Email**     | Valid email format                   | `forms.EmailField`     |
| **Username**  | Start with letters, end with numbers | `^[a-zA-Z]+\d+$`       |
| **Password**  | More than 8 characters               | `len(password) > 8`    |

---

### Username Validation Regex Explained

```python
username_regex = r'^[a-zA-Z]+\d+$'
```

| Part        | Meaning                                      |
| ----------- | -------------------------------------------- |
| `^`         | Start of string                              |
| `[a-zA-Z]+` | One or more letters (uppercase or lowercase) |
| `\d+`       | One or more digits                           |
| `$`         | End of string                                |

**Valid Examples:** john123, User99, admin1, TestUser2024

**Invalid Examples:** 123john, john, 12345, john_123, john123abc

---

---

## Q4: Project File Upload Form with Validation

**Question:** Design following form in HTML and write corresponding server-side script to upload and submit user's data into database in consideration of following validation rules.

**Form fields:** TU Registration Number, Email Address, Upload your Project File

**Validation rules:**

- Registration number, email and upload file are mandatory field
- Email address should be a proper email format
- Upload file format must include pdf, doc, docx, ppt, pptx, jpeg file format
- File size must be less than 5MB

---

### Solution

**Step 1: Create Project Submission Model**

```python
# models.py
from django.db import models


class ProjectSubmission(models.Model):
    tu_registration_number = models.CharField(max_length=50, unique=True)
    email = models.EmailField()
    project_file = models.FileField(upload_to='projects/')
    uploaded_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.tu_registration_number} - {self.email}"
```

---

**Step 2: Configure Media Settings**

```python
# settings.py (add at bottom)
import os

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

---

**Step 3: Run Migrations**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

**Step 4: Create Django Form with Validation**

```python
# forms.py
from django import forms


class ProjectSubmissionForm(forms.Form):
    tu_registration_number = forms.CharField(
        max_length=50,
        error_messages={'required': 'TU Registration Number is required'}
    )

    email = forms.EmailField(
        error_messages={
            'required': 'Email Address is required',
            'invalid': 'Please enter a valid email address'
        }
    )

    project_file = forms.FileField(
        error_messages={'required': 'Project File is required'}
    )

    def clean_project_file(self):
        """Validate file type and size"""
        project_file = self.cleaned_data.get('project_file')

        if not project_file:
            raise forms.ValidationError('Project File is required')

        # Allowed file extensions
        allowed_extensions = ['pdf', 'doc', 'docx', 'ppt', 'pptx', 'jpeg', 'jpg']
        file_extension = project_file.name.split('.')[-1].lower()

        if file_extension not in allowed_extensions:
            raise forms.ValidationError(
                'File format must be pdf, doc, docx, ppt, pptx, or jpeg'
            )

        # Check file size (max 5MB)
        max_size = 5 * 1024 * 1024  # 5MB in bytes
        if project_file.size > max_size:
            raise forms.ValidationError(
                'File size must be less than 5MB'
            )

        return project_file
```

---

**Step 5: Create View**

```python
# views.py
from django.shortcuts import render, redirect
from django.contrib import messages
from .forms import ProjectSubmissionForm
from .models import ProjectSubmission


def project_upload(request):
    if request.method == 'POST':
        form = ProjectSubmissionForm(request.POST, request.FILES)

        if form.is_valid():
            # Check if registration number already exists
            reg_number = form.cleaned_data['tu_registration_number']
            if ProjectSubmission.objects.filter(tu_registration_number=reg_number).exists():
                form.add_error('tu_registration_number', 'This registration number already submitted')
                return render(request, 'project_form.html', {'form': form})

            # Create project submission record
            submission = ProjectSubmission(
                tu_registration_number=reg_number,
                email=form.cleaned_data['email'],
                project_file=form.cleaned_data['project_file'],
            )
            submission.save()

            messages.success(request, 'Project submitted successfully!')
            return redirect('submission_list')
    else:
        form = ProjectSubmissionForm()

    return render(request, 'project_form.html', {'form': form})


def submission_list(request):
    submissions = ProjectSubmission.objects.all()
    return render(request, 'submission_list.html', {'submissions': submissions})
```

---

**Step 6: Create Project Upload Form Template (HTML)**

```html
<!-- templates/project_form.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Project Submission</title>
    <style>
      .error {
        color: red;
        font-size: 0.9em;
      }
      .form-group {
        margin-bottom: 15px;
      }
      label {
        display: block;
        margin-bottom: 5px;
      }
      input {
        padding: 8px;
        width: 300px;
      }
      .hint {
        color: gray;
        font-size: 0.8em;
      }
    </style>
  </head>
  <body>
    <h1>Project File Submission</h1>

    {% if messages %} {% for message in messages %}
    <p style="color: green;">{{ message }}</p>
    {% endfor %} {% endif %}

    <form method="POST" enctype="multipart/form-data">
      {% csrf_token %}

      <div class="form-group">
        <label>TU Registration Number: *</label>
        <input
          type="text"
          name="tu_registration_number"
          value="{{ form.tu_registration_number.value|default:'' }}"
        />
        {% if form.tu_registration_number.errors %}
        <p class="error">{{ form.tu_registration_number.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Email Address: *</label>
        <input
          type="email"
          name="email"
          value="{{ form.email.value|default:'' }}"
        />
        {% if form.email.errors %}
        <p class="error">{{ form.email.errors.0 }}</p>
        {% endif %}
      </div>

      <div class="form-group">
        <label>Upload your Project File: *</label>
        <input
          type="file"
          name="project_file"
          accept=".pdf,.doc,.docx,.ppt,.pptx,.jpeg,.jpg"
        />
        <p class="hint">Allowed: PDF, DOC, DOCX, PPT, PPTX, JPEG (Max 5MB)</p>
        {% if form.project_file.errors %}
        <p class="error">{{ form.project_file.errors.0 }}</p>
        {% endif %}
      </div>

      <button type="submit">Submit Project</button>
    </form>
  </body>
</html>
```

---

**Step 7: Create Submission List Template**

```html
<!-- templates/submission_list.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Submissions List</title>
  </head>
  <body>
    <h1>Project Submissions</h1>
    <a href="{% url 'project_upload' %}">+ Submit New Project</a>

    <table border="1" cellpadding="10" style="margin-top: 20px;">
      <tr>
        <th>Registration No.</th>
        <th>Email</th>
        <th>Project File</th>
        <th>Uploaded At</th>
      </tr>
      {% for submission in submissions %}
      <tr>
        <td>{{ submission.tu_registration_number }}</td>
        <td>{{ submission.email }}</td>
        <td>
          <a href="{{ submission.project_file.url }}" target="_blank"
            >View File</a
          >
        </td>
        <td>{{ submission.uploaded_at }}</td>
      </tr>
      {% empty %}
      <tr>
        <td colspan="4">No submissions yet.</td>
      </tr>
      {% endfor %}
    </table>
  </body>
</html>
```

---

**Step 8: Configure URLs**

```python
# urls.py
from django.urls import path
from django.conf import settings
from django.conf.urls.static import static
from . import views

urlpatterns = [
    path('project/submit/', views.project_upload, name='project_upload'),
    path('submissions/', views.submission_list, name='submission_list'),
]

# Serve media files during development
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

### Validation Rules Summary

| Field                      | Validation Rule                      |
| -------------------------- | ------------------------------------ |
| **TU Registration Number** | Required, unique                     |
| **Email Address**          | Required, valid email format         |
| **Project File**           | Required                             |
| **File Format**            | pdf, doc, docx, ppt, pptx, jpeg only |
| **File Size**              | Must be less than 5MB                |

---

### File Validation Code Explained

```python
# Allowed file extensions
allowed_extensions = ['pdf', 'doc', 'docx', 'ppt', 'pptx', 'jpeg', 'jpg']
file_extension = project_file.name.split('.')[-1].lower()

if file_extension not in allowed_extensions:
    raise forms.ValidationError('File format must be pdf, doc, docx, ppt, pptx, or jpeg')

# Check file size (max 5MB)
max_size = 5 * 1024 * 1024  # 5MB = 5 * 1024 * 1024 bytes
if project_file.size > max_size:
    raise forms.ValidationError('File size must be less than 5MB')
```

---

### Database Schema

```
ProjectSubmission Table:
+------------------------+--------------+
| Column                 | Type         |
+------------------------+--------------+
| id                     | INTEGER (PK) |
| tu_registration_number | VARCHAR(50)  |
| email                  | VARCHAR(254) |
| project_file           | VARCHAR(100) |
| uploaded_at            | DATETIME     |
+------------------------+--------------+
```

---

### Important Notes

1. **File Storage**: Files are stored in `media/projects/` directory
2. **enctype**: Form must have `enctype="multipart/form-data"` for file uploads
3. **request.FILES**: File data is accessed via `request.FILES`, not `request.POST`
4. **Media URL**: Configure `MEDIA_URL` and `MEDIA_ROOT` in settings.py

---

---

## Q5: Convert PHP CRUD to Django (Note Taking App)

**Question:** Write HTML, PHP and SQL code for CRUD operations. Convert to Django solution.

**Original:** PHP + MySQL Note Taking Web App

---

### Django Solution

**Step 1: Create Django Project and App**

```bash
# Create project
django-admin startproject config .

# Create app
python manage.py startapp notes
```

---

**Step 2: Register App in Settings**

```python
# config/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'notes',  # Add this
]
```

---

**Step 3: Create Note Model**

```python
# notes/models.py
from django.db import models


class Note(models.Model):
    title = models.CharField(max_length=100)
    description = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title

    class Meta:
        ordering = ['-id']  # Latest first (like PHP: ORDER BY id DESC)
```

**Equivalent SQL:**

```sql
CREATE TABLE `notes` (
  `id` INT AUTO_INCREMENT PRIMARY KEY,
  `title` varchar(100) NOT NULL,
  `description` text NOT NULL,
  `created_at` datetime NOT NULL
);
```

---

**Step 4: Run Migrations**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

**Step 5: Create Views (CRUD Operations)**

```python
# notes/views.py
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib import messages
from .models import Note


# READ - Display all notes (equivalent to index.php)
def index(request):
    notes = Note.objects.all()  # Already ordered by -id in model Meta
    return render(request, 'notes/index.html', {'notes': notes})


# CREATE - Add new note (equivalent to add.html + addAction.php)
def add_note(request):
    if request.method == 'POST':
        title = request.POST.get('title', '').strip()
        description = request.POST.get('description', '').strip()

        # Validation
        errors = []
        if not title:
            errors.append('Title field is empty.')
        if not description:
            errors.append('Description field is empty.')

        if errors:
            return render(request, 'notes/add.html', {'errors': errors})

        # Create note
        Note.objects.create(title=title, description=description)
        messages.success(request, 'Data added successfully!')
        return redirect('notes:index')

    return render(request, 'notes/add.html')


# UPDATE - Edit note (equivalent to edit.php + editAction.php)
def edit_note(request, note_id):
    note = get_object_or_404(Note, id=note_id)

    if request.method == 'POST':
        title = request.POST.get('title', '').strip()
        description = request.POST.get('description', '').strip()

        # Validation
        errors = []
        if not title:
            errors.append('Title field is empty.')
        if not description:
            errors.append('Description field is empty.')

        if errors:
            return render(request, 'notes/edit.html', {
                'note': note,
                'errors': errors
            })

        # Update note
        note.title = title
        note.description = description
        note.save()
        messages.success(request, 'Data updated successfully!')
        return redirect('notes:index')

    return render(request, 'notes/edit.html', {'note': note})


# DELETE - Delete note (equivalent to deleteAction.php)
def delete_note(request, note_id):
    note = get_object_or_404(Note, id=note_id)
    note.delete()
    messages.success(request, 'Note deleted successfully!')
    return redirect('notes:index')
```

---

**Step 6: Create Templates**

**Create `notes/templates/notes/index.html`** (equivalent to index.php)

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Homepage</title>
    <style>
      table,
      th,
      td {
        border: 1px solid black;
        border-collapse: collapse;
      }
      th,
      td {
        padding: 10px;
      }
      .success {
        color: green;
      }
    </style>
  </head>
  <body>
    <h2>Homepage</h2>
    <p>
      <a href="{% url 'notes:add' %}">Add New Data</a>
    </p>

    {% if messages %} {% for message in messages %}
    <p class="success">{{ message }}</p>
    {% endfor %} {% endif %}

    <table width="80%">
      <tr>
        <th>Title</th>
        <th>Description</th>
        <th>Action</th>
      </tr>
      {% for note in notes %}
      <tr>
        <td>{{ note.title }}</td>
        <td>{{ note.description }}</td>
        <td>
          <a href="{% url 'notes:edit' note.id %}">Edit</a>
          |
          <a
            href="{% url 'notes:delete' note.id %}"
            onclick="return confirm('Are you sure to delete?');"
            >Delete</a
          >
        </td>
      </tr>
      {% empty %}
      <tr>
        <td colspan="3">No results Found</td>
      </tr>
      {% endfor %}
    </table>
  </body>
</html>
```

---

**Create `notes/templates/notes/add.html`** (equivalent to add.html)

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Add Notes</title>
    <style>
      .error {
        color: red;
      }
    </style>
  </head>
  <body>
    <h2>Add Notes</h2>
    <p>
      <a href="{% url 'notes:index' %}">Home</a>
    </p>

    {% if errors %} {% for error in errors %}
    <p class="error">{{ error }}</p>
    {% endfor %} {% endif %}

    <form action="{% url 'notes:add' %}" method="post">
      {% csrf_token %}
      <label for="title">Title: </label>
      <input type="text" name="title" id="title" />
      <br /><br />
      <label for="description">Description: </label>
      <textarea name="description" id="description"></textarea>
      <br /><br />
      <input type="submit" value="Submit" />
    </form>
  </body>
</html>
```

---

**Create `notes/templates/notes/edit.html`** (equivalent to edit.php)

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Edit Note</title>
    <style>
      .error {
        color: red;
      }
    </style>
  </head>
  <body>
    <h2>Edit Note</h2>
    <p>
      <a href="{% url 'notes:index' %}">Home</a>
    </p>

    {% if errors %} {% for error in errors %}
    <p class="error">{{ error }}</p>
    {% endfor %} {% endif %}

    <form method="post">
      {% csrf_token %}
      <label>Title: </label>
      <input type="text" name="title" value="{{ note.title }}" />
      <br /><br />
      <label>Description: </label>
      <textarea name="description">{{ note.description }}</textarea>
      <br /><br />
      <input type="submit" value="Update" />
    </form>
  </body>
</html>
```

---

**Step 7: Configure URLs**

**Create `notes/urls.py`**

```python
from django.urls import path
from . import views

app_name = 'notes'

urlpatterns = [
    path('', views.index, name='index'),
    path('add/', views.add_note, name='add'),
    path('edit/<int:note_id>/', views.edit_note, name='edit'),
    path('delete/<int:note_id>/', views.delete_note, name='delete'),
]
```

**Update `config/urls.py`**

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('notes.urls')),
]
```

---

**Step 8: Run the Server**

```bash
python manage.py runserver
```

Visit `http://127.0.0.1:8000/` to see the Note Taking App.

---

---

### Register Model in Admin (Optional)

```python
# notes/admin.py
from django.contrib import admin
from .models import Note


@admin.register(Note)
class NoteAdmin(admin.ModelAdmin):
    list_display = ['title', 'description', 'created_at']
    search_fields = ['title', 'description']
```

---

---

## Q6: File Upload with Extension and Size Validation

**Question:** Write a Django view to upload a file and validate:

- File extension (only allow image files: jpg, jpeg, png, gif)
- File size (must be less than 2MB)

---

### Solution

**Step 1: Create Model**

```python
# models.py
from django.db import models


class UploadedFile(models.Model):
    file = models.FileField(upload_to='uploads/')
    uploaded_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.file.name
```

---

**Step 2: Create Form with Validation**

```python
# forms.py
from django import forms


class FileUploadForm(forms.Form):
    file = forms.FileField(
        error_messages={'required': 'Please select a file to upload'}
    )

    def clean_file(self):
        file = self.cleaned_data.get('file')

        if not file:
            raise forms.ValidationError('Please select a file to upload')

        # Validate file extension
        allowed_extensions = ['jpg', 'jpeg', 'png', 'gif']
        file_extension = file.name.split('.')[-1].lower()

        if file_extension not in allowed_extensions:
            raise forms.ValidationError(
                f'Invalid file type. Allowed: {", ".join(allowed_extensions)}'
            )

        # Validate file size (max 2MB)
        max_size = 2 * 1024 * 1024  # 2MB in bytes

        if file.size > max_size:
            raise forms.ValidationError(
                'File size must be less than 2MB'
            )

        return file
```

---

**Step 3: Create View**

```python
# views.py
from django.shortcuts import render, redirect
from django.contrib import messages
from .forms import FileUploadForm
from .models import UploadedFile


def upload_file(request):
    if request.method == 'POST':
        form = FileUploadForm(request.POST, request.FILES)

        if form.is_valid():
            # Save file to database
            uploaded_file = UploadedFile(
                file=form.cleaned_data['file']
            )
            uploaded_file.save()

            messages.success(request, 'File uploaded successfully!')
            return redirect('upload_success')
    else:
        form = FileUploadForm()

    return render(request, 'upload.html', {'form': form})


def upload_success(request):
    files = UploadedFile.objects.all().order_by('-uploaded_at')
    return render(request, 'success.html', {'files': files})
```

---

**Step 4: Create Upload Template**

```html
<!-- templates/upload.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>File Upload</title>
    <style>
      .error {
        color: red;
      }
      .form-group {
        margin-bottom: 15px;
      }
    </style>
  </head>
  <body>
    <h2>Upload File</h2>

    <form method="POST" enctype="multipart/form-data">
      {% csrf_token %}

      <div class="form-group">
        <label>Select File:</label><br />
        <input type="file" name="file" accept=".jpg,.jpeg,.png,.gif" />
        <p style="color: gray; font-size: 0.9em;">
          Allowed: JPG, JPEG, PNG, GIF (Max 2MB)
        </p>
        {% if form.file.errors %}
        <p class="error">{{ form.file.errors.0 }}</p>
        {% endif %}
      </div>

      <button type="submit">Upload</button>
    </form>
  </body>
</html>
```

---

**Step 5: Create Success Template**

```html
<!-- templates/success.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Upload Success</title>
  </head>
  <body>
    <h2>Uploaded Files</h2>
    <a href="{% url 'upload_file' %}">Upload Another</a>

    <ul>
      {% for file in files %}
      <li>
        <a href="{{ file.file.url }}" target="_blank">{{ file.file.name }}</a>
        ({{ file.uploaded_at }})
      </li>
      {% empty %}
      <li>No files uploaded yet.</li>
      {% endfor %}
    </ul>
  </body>
</html>
```

---

**Step 6: Configure URLs**

```python
# urls.py
from django.urls import path
from django.conf import settings
from django.conf.urls.static import static
from . import views

urlpatterns = [
    path('upload/', views.upload_file, name='upload_file'),
    path('success/', views.upload_success, name='upload_success'),
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

**Step 7: Configure Media Settings**

```python
# settings.py
import os

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

---

### File Validation Summary

| Validation    | Rule                     | Code                                       |
| ------------- | ------------------------ | ------------------------------------------ |
| **Extension** | jpg, jpeg, png, gif only | `file_extension not in allowed_extensions` |
| **Size**      | Less than 2MB            | `file.size > max_size`                     |

---

### Key Validation Code

```python
def clean_file(self):
    file = self.cleaned_data.get('file')

    # 1. Validate extension
    allowed_extensions = ['jpg', 'jpeg', 'png', 'gif']
    file_extension = file.name.split('.')[-1].lower()

    if file_extension not in allowed_extensions:
        raise forms.ValidationError('Invalid file type')

    # 2. Validate size (2MB max)
    max_size = 2 * 1024 * 1024  # 2MB = 2 * 1024 * 1024 bytes

    if file.size > max_size:
        raise forms.ValidationError('File size must be less than 2MB')

    return file
```

---

### Size Calculation Reference

| Size | Bytes                       |
| ---- | --------------------------- |
| 1 KB | 1024                        |
| 1 MB | 1024 \* 1024 = 1,048,576    |
| 2 MB | 2 _ 1024 _ 1024 = 2,097,152 |
| 5 MB | 5 _ 1024 _ 1024 = 5,242,880 |

---

---

## Q7: Sessions and Cookies in Django

**Question:** How to create, store, access and destroy session in PHP? Convert to Django version.

---

### Django Cookies

**Setting Cookies:**

```python
# views.py
from django.http import HttpResponse


def set_cookie(request):
    response = HttpResponse("Cookie Set!")

    # Set a cookie (expires in 30 days)
    response.set_cookie('user', 'John Doe', max_age=86400 * 30)  # 30 days

    # Set multiple cookies
    response.set_cookie('theme', 'dark', max_age=86400 * 30)
    response.set_cookie('language', 'en', max_age=86400 * 30)

    # Secure cookie (HTTPS only, HttpOnly, SameSite)
    # response.set_cookie(
    #     'username',
    #     username,
    #     max_age=86400 * 30,
    #     secure=True,       # Only send over HTTPS
    #     httponly=True,     # JavaScript cannot access
    #     samesite='Strict'  # CSRF protection
    # )

    return response
```

**PHP vs Django Cookie Comparison:**

| PHP                                         | Django                                               |
| ------------------------------------------- | ---------------------------------------------------- |
| `setcookie("user", "John", time() + 86400)` | `response.set_cookie('user', 'John', max_age=86400)` |
| `$_COOKIE['user']`                          | `request.COOKIES.get('user')`                        |
| `setcookie("user", "", time() - 3600)`      | `response.delete_cookie('user')`                     |

---

**Accessing Cookies:**

```python
# views.py
from django.http import HttpResponse


def get_cookie(request):
    # Get single cookie
    user = request.COOKIES.get('user')

    if user:
        return HttpResponse(f"Welcome {user}")
    else:
        return HttpResponse("Cookie not set.")


def show_all_cookies(request):
    # Display all cookies
    output = ""
    for key, value in request.COOKIES.items():
        output += f"{key}: {value}<br>"

    return HttpResponse(output)
```

---

**Deleting Cookies:**

```python
# views.py
from django.http import HttpResponse


def delete_cookie(request):
    response = HttpResponse("Cookie Deleted!")

    # Delete cookie
    response.delete_cookie('user')

    return response
```

---

### Django Sessions

Django sessions are enabled by default. Data is stored on the server.

**Session Settings (settings.py):**

```python
# Already included by default in INSTALLED_APPS:
# 'django.contrib.sessions',

# Already included in MIDDLEWARE:
# 'django.contrib.sessions.middleware.SessionMiddleware',

# Optional session settings:
SESSION_COOKIE_AGE = 86400 * 30  # 30 days (default: 2 weeks)
SESSION_EXPIRE_AT_BROWSER_CLOSE = False  # Keep session after browser closes
```

---

**Creating Session (Login):**

```python
# views.py
from django.shortcuts import render, redirect


def login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')

        # Dummy authentication check
        if username == 'admin' and password == 'password':
            # Create session
            request.session['username'] = username

            # Redirect to dashboard
            return redirect('dashboard')
        else:
            return render(request, 'login.html', {
                'error': 'Invalid credentials.'
            })

    return render(request, 'login.html')
```

**Login Template (login.html):**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Login</title>
  </head>
  <body>
    {% if error %}
    <p style="color: red;">{{ error }}</p>
    {% endif %}

    <form method="post">
      {% csrf_token %} Username:
      <input type="text" name="username" required /><br /><br />
      Password: <input type="password" name="password" required /><br /><br />
      <input type="submit" value="Login" />
    </form>
  </body>
</html>
```

---

**Accessing Session (Dashboard):**

```python
# views.py
from django.shortcuts import render, redirect


def dashboard(request):
    # Check if user is logged in
    if 'username' not in request.session:
        return redirect('login')

    username = request.session['username']
    return render(request, 'dashboard.html', {'username': username})
```

**Dashboard Template (dashboard.html):**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Dashboard</title>
  </head>
  <body>
    <h1>Welcome, {{ username }}!</h1>
    <a href="{% url 'logout' %}">Logout</a>
  </body>
</html>
```

---

**Destroying Session (Logout):**

```python
# views.py
from django.shortcuts import redirect


def logout(request):
    # Clear all session data (like session_unset + session_destroy)
    request.session.flush()

    # Redirect to login
    return redirect('login')
```

---

**URL Configuration:**

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('login/', views.login, name='login'),
    path('dashboard/', views.dashboard, name='dashboard'),
    path('logout/', views.logout, name='logout'),
]
```

---

### PHP to Django Session Comparison

| PHP                              | Django                                |
| -------------------------------- | ------------------------------------- |
| `session_start()`                | Automatic (middleware)                |
| `$_SESSION['username'] = $value` | `request.session['username'] = value` |
| `$_SESSION['username']`          | `request.session.get('username')`     |
| `isset($_SESSION['username'])`   | `'username' in request.session`       |
| `session_unset()`                | `request.session.clear()`             |
| `session_destroy()`              | `request.session.flush()`             |
| `unset($_SESSION['username'])`   | `del request.session['username']`     |

---

### Session Methods Summary

| Method                                | Description                                          |
| ------------------------------------- | ---------------------------------------------------- |
| `request.session['key'] = value`      | Set session variable                                 |
| `request.session.get('key')`          | Get session variable (returns None if not exists)    |
| `request.session['key']`              | Get session variable (raises KeyError if not exists) |
| `'key' in request.session`            | Check if key exists                                  |
| `del request.session['key']`          | Delete specific key                                  |
| `request.session.clear()`             | Clear all session data (like `session_unset`)        |
| `request.session.flush()`             | Destroy session completely (like `session_destroy`)  |
| `request.session.set_expiry(seconds)` | Set session expiry                                   |

---

### Cookie Methods Summary

| Method                                              | Description                                |
| --------------------------------------------------- | ------------------------------------------ |
| `response.set_cookie('key', 'value')`               | Set cookie                                 |
| `response.set_cookie('key', 'value', max_age=3600)` | Set cookie with expiry (seconds)           |
| `request.COOKIES.get('key')`                        | Get cookie value                           |
| `request.COOKIES['key']`                            | Get cookie (raises KeyError if not exists) |
| `response.delete_cookie('key')`                     | Delete cookie                              |

---

### Complete Login/Logout Example

**views.py:**

```python
from django.shortcuts import render, redirect


def login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')

        if username == 'admin' and password == 'password':
            request.session['username'] = username
            return redirect('dashboard')
        else:
            return render(request, 'login.html', {'error': 'Invalid credentials.'})

    return render(request, 'login.html')


def dashboard(request):
    if 'username' not in request.session:
        return redirect('login')

    return render(request, 'dashboard.html', {
        'username': request.session['username']
    })


def logout(request):
    request.session.flush()
    return redirect('login')
```

---

### Key Differences

| Aspect              | PHP                      | Django                        |
| ------------------- | ------------------------ | ----------------------------- |
| **Session Start**   | Manual `session_start()` | Automatic via middleware      |
| **Session Storage** | File-based by default    | Database by default           |
| **Cookie Access**   | `$_COOKIE['name']`       | `request.COOKIES.get('name')` |
| **Session Access**  | `$_SESSION['name']`      | `request.session['name']`     |
| **Destroy Session** | `session_destroy()`      | `request.session.flush()`     |
| **CSRF Protection** | Manual                   | Built-in `{% csrf_token %}`   |

---

---

---

### Session vs Cookie: The Core Differences

Although both can store data and expire, they store data in different **locations**:

| Feature              | Cookie                                  | Session                           |
| :------------------- | :-------------------------------------- | :-------------------------------- |
| **Storage Location** | **Client-side** (Browser)               | **Server-side** (Database/File)   |
| **What is Stored?**  | Actual string data (e.g., `theme=dark`) | Usually just a **Session ID**     |
| **Security**         | **Low** (User can view/edit)            | **High** (Data stays on server)   |
| **Capacity**         | Small (~4KB)                            | Large (Limited by server)         |
| **Performance**      | Sent with every request                 | Retrieved from server when needed |

**Analogy:**

- **Cookie (Key Card):** You carry it with you. It grants access but doesn't contain your stuff. If lost, anyone can use it (until it expires).
- **Session (Hotel Room):** Your stuff (data) is stored securely here. You use the Key Card (Cookie) to enter your Room (Session).

---

### Important Distinction: Browser Storage vs Server Session

It is common to confuse **Server-Side Sessions** with **Browser Session Storage**. They are **NOT** the same thing.

| Feature             | **Cookie**                   | **Browser Session Storage**                                         | **Server Session (Django/PHP)** |
| :------------------ | :--------------------------- | :------------------------------------------------------------------ | :------------------------------ |
| **Role**            | **The Transporter**          | **Local Notepad**                                                   | **The Vault**                   |
| **What it holds**   | **Session ID** (`sessionid`) | JS Data (e.g., Form inputs) i.e. Temp UI state (e.g. form progress) | **Actual User Data**            |
| **Sent to Server?** | **YES (Automatically)**      | **NO (Never)**                                                      | **N/A (Lives on Server)**       |
| **Lifespan**        | Set expiry date              | Until **Tab Closes**                                                | Until Logout/Expiry             |

**Key Takeaway:**

- The **Cookie** stores the **Session ID**.
- The **Server** uses that ID to unlock the **Session**.
- **Browser Session Storage** is just for temporary client-side data and has **nothing to do with authentication**.

---

## Q8: Basics of Django Forms

**Django Forms** handle HTML form rendering, data validation, and cleaning.

### 1. Types of Forms

1. **`forms.Form`**: Standard form. You define fields manually. Used for non-model data (e.g., contact form, search).
2. **`forms.ModelForm`**: Connected to a database model. Automatically generates fields based on the model.

---

### 2. Creating a Form

**Manual Form (`forms.Form`):**

```python
# forms.py
from django import forms

class ContactForm(forms.Form):
    # Define fields manually
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
```

**Model Form (`forms.ModelForm`):**

```python
# forms.py
from django import forms
from .models import Student

class StudentForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = ['name', 'roll_no', 'email']  # OR '__all__'
```

---

### 3. Rendering in Template

You must use `method="POST"` and `{% csrf_token %}`.

```html
<form method="POST">
  {% csrf_token %}

  <!-- Option 1: Render paragraph for each field -->
  {{ form.as_p }}

  <!-- Option 2: Render table rows -->
  {{ form.as_table }}

  <!-- Option 3: Render list items -->
  {{ form.as_ul }}

  <!-- Option 4: Manual Rendering (Best Control) -->
  <div class="form-group">
    <label for="{{ form.name.id_for_label }}">Name:</label>
    {{ form.name }} {{ form.name.errors }}
  </div>

  <button type="submit">Submit</button>
</form>
```

---

### 4. Handling in Views

Standard pattern for processing forms:

```python
# views.py
from django.shortcuts import render, redirect
from .forms import ContactForm

def contact_view(request):
    if request.method == 'POST':
        # 1. Bind data to form
        form = ContactForm(request.POST)

        # 2. Validate
        if form.is_valid():
            # 3. Get cleaned data
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']

            # 4. Save or process
            # (send email, save to db, etc.)

            return redirect('success_page')
    else:
        # GET request: Display empty form
        form = ContactForm()

    return render(request, 'contact.html', {'form': form})
```

---

### 5. Validation

Validation happens when you call `form.is_valid()`.

**A. Built-in Validation:**

- `required=True` (default)
- `max_length`, `min_length`
- Email format, URL format, etc.

**B. Custom Field Validation (`clean_<fieldname>`):**

```python
def clean_email(self):
    email = self.cleaned_data['email']
    if not email.endswith('@example.com'):
        raise forms.ValidationError("Only example.com emails are allowed!")
    return email
```

**C. Custom Cross-Field Validation (`clean`):**

```python
def clean(self):
    cleaned_data = super().clean()
    password = cleaned_data.get("password")
    confirm_password = cleaned_data.get("confirm_password")

    if password != confirm_password:
        raise forms.ValidationError("Passwords do not match")
```

---

### 6. Widgets

Widgets control how the HTML is rendered (e.g., `<input>` vs `<textarea>`).

```python
name = forms.CharField(
    widget=forms.TextInput(attrs={'class': 'form-control', 'placeholder': 'Enter Name'})
)

password = forms.CharField(
    widget=forms.PasswordInput()  # Renders as type="password"
)

birth_date = forms.DateField(
    widget=forms.DateInput(attrs={'type': 'date'})  # HTML5 Date Picker
)
```

---

## Basics of Django Forms

Django forms provide a powerful way to handle user input, validate data, and render HTML forms. Forms in Django help you avoid writing repetitive HTML form code and provide built-in validation mechanisms.

---

### Why Use Django Forms?

| Benefit                 | Description                                 |
| ----------------------- | ------------------------------------------- |
| **Automatic Rendering** | Forms can render themselves as HTML         |
| **Validation**          | Built-in and custom validation support      |
| **Security**            | CSRF protection, XSS prevention             |
| **Data Binding**        | Easy binding of form data to Python objects |
| **Error Handling**      | Automatic error message handling            |

---

### Types of Django Forms

Django provides two main types of forms:

1. **`forms.Form`** - Regular forms (not tied to any model)
2. **`forms.ModelForm`** - Forms that are automatically generated from models

---

### 1. Creating a Regular Form (`forms.Form`)

Regular forms are created by subclassing `forms.Form`. Each form field corresponds to an HTML input element.

```python
# forms.py
from django import forms


class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
    phone = forms.CharField(max_length=15, required=False)
```

---

### 2. Creating a ModelForm (`forms.ModelForm`)

ModelForms automatically create form fields based on model fields. This reduces code duplication.

```python
# models.py
from django.db import models


class Student(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    roll_number = models.IntegerField()
    department = models.CharField(max_length=50)
```

```python
# forms.py
from django import forms
from .models import Student


class StudentForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = ['name', 'email', 'roll_number', 'department']
        # Or use '__all__' to include all fields
        # fields = '__all__'

        # Exclude specific fields
        # exclude = ['created_at']
```

---

### Common Form Fields

| Field Type            | Description                  | HTML Element                    |
| --------------------- | ---------------------------- | ------------------------------- |
| `CharField`           | Text input                   | `<input type="text">`           |
| `EmailField`          | Email input with validation  | `<input type="email">`          |
| `IntegerField`        | Integer input                | `<input type="number">`         |
| `FloatField`          | Decimal number input         | `<input type="number">`         |
| `DateField`           | Date input                   | `<input type="date">`           |
| `DateTimeField`       | Date and time input          | `<input type="datetime-local">` |
| `BooleanField`        | Checkbox                     | `<input type="checkbox">`       |
| `ChoiceField`         | Dropdown select              | `<select>`                      |
| `MultipleChoiceField` | Multiple selection           | `<select multiple>`             |
| `FileField`           | File upload                  | `<input type="file">`           |
| `ImageField`          | Image upload with validation | `<input type="file">`           |

---

### Form Field Arguments

Common arguments that can be passed to form fields:

```python
from django import forms


class ExampleForm(forms.Form):
    # required - is the field mandatory? (default True)
    name = forms.CharField(required=True)

    # max_length - maximum character length
    username = forms.CharField(max_length=50)

    # min_length - minimum character length
    password = forms.CharField(min_length=8)

    # initial - default value
    country = forms.CharField(initial='Nepal')

    # help_text - helper text for the field
    email = forms.EmailField(help_text='Enter a valid email address')

    # label - custom label for the field
    dob = forms.DateField(label='Date of Birth')

    # error_messages - custom error messages
    phone = forms.CharField(
        error_messages={
            'required': 'Phone number is required',
            'max_length': 'Phone number too long'
        }
    )

    # disabled - make field read-only
    id_number = forms.CharField(disabled=True)
```

---

### Widgets

Widgets control how form fields are rendered in HTML. You can customize the appearance using widgets.

```python
from django import forms


class ContactForm(forms.Form):
    # Textarea widget for multiline text
    message = forms.CharField(widget=forms.Textarea)

    # Password input
    password = forms.CharField(widget=forms.PasswordInput)

    # Hidden input
    token = forms.CharField(widget=forms.HiddenInput)

    # Date input
    birth_date = forms.DateField(widget=forms.DateInput(attrs={'type': 'date'}))

    # Select/Dropdown
    CHOICES = [('M', 'Male'), ('F', 'Female'), ('O', 'Other')]
    gender = forms.ChoiceField(widget=forms.Select, choices=CHOICES)

    # Radio buttons
    priority = forms.ChoiceField(
        widget=forms.RadioSelect,
        choices=[('low', 'Low'), ('medium', 'Medium'), ('high', 'High')]
    )

    # Checkboxes for multiple choices
    hobbies = forms.MultipleChoiceField(
        widget=forms.CheckboxSelectMultiple,
        choices=[('reading', 'Reading'), ('sports', 'Sports'), ('music', 'Music')]
    )

    # Adding CSS classes and attributes
    email = forms.EmailField(
        widget=forms.EmailInput(attrs={
            'class': 'form-control',
            'placeholder': 'Enter your email',
            'id': 'email-field'
        })
    )
```

---

### Form Validation

#### Built-in Validation

Django provides automatic validation based on field types:

```python
from django import forms


class RegistrationForm(forms.Form):
    email = forms.EmailField()  # Validates email format
    age = forms.IntegerField(min_value=18, max_value=100)  # Range validation
    website = forms.URLField()  # Validates URL format
```

---

#### Custom Field Validation (`clean_<fieldname>`)

Create custom validation for specific fields using `clean_<fieldname>()` methods:

```python
from django import forms
import re


class UserForm(forms.Form):
    username = forms.CharField(max_length=50)
    password = forms.CharField(widget=forms.PasswordInput)
    confirm_password = forms.CharField(widget=forms.PasswordInput)
    phone = forms.CharField(max_length=10)

    def clean_username(self):
        """Username must start with a letter"""
        username = self.cleaned_data.get('username')
        if not username[0].isalpha():
            raise forms.ValidationError('Username must start with a letter')
        return username

    def clean_phone(self):
        """Phone must be 10 digits starting with 98, 97, or 96"""
        phone = self.cleaned_data.get('phone')
        if not re.match(r'^(98|97|96)\d{8}$', phone):
            raise forms.ValidationError('Invalid phone number format')
        return phone

    def clean(self):
        """Cross-field validation - validate multiple fields together"""
        cleaned_data = super().clean()
        password = cleaned_data.get('password')
        confirm_password = cleaned_data.get('confirm_password')

        if password and confirm_password:
            if password != confirm_password:
                raise forms.ValidationError('Passwords do not match')

        return cleaned_data
```

---

### Using Forms in Views

#### Function-Based View

```python
# views.py
from django.shortcuts import render, redirect
from .forms import ContactForm


def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)

        if form.is_valid():
            # Access cleaned data
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']
            message = form.cleaned_data['message']

            # Process the data (save to database, send email, etc.)
            # ...

            return redirect('success')
    else:
        form = ContactForm()

    return render(request, 'contact.html', {'form': form})
```

---

#### Using ModelForm to Create/Update Records

```python
# views.py
from django.shortcuts import render, redirect, get_object_or_404
from .forms import StudentForm
from .models import Student


def create_student(request):
    if request.method == 'POST':
        form = StudentForm(request.POST)
        if form.is_valid():
            form.save()  # Automatically saves to database
            return redirect('student_list')
    else:
        form = StudentForm()

    return render(request, 'student_form.html', {'form': form})


def update_student(request, pk):
    student = get_object_or_404(Student, pk=pk)

    if request.method == 'POST':
        form = StudentForm(request.POST, instance=student)
        if form.is_valid():
            form.save()
            return redirect('student_list')
    else:
        form = StudentForm(instance=student)

    return render(request, 'student_form.html', {'form': form})
```

---

### Rendering Forms in Templates

There are multiple ways to render forms in Django templates:

#### Method 1: Auto Rendering

```html
<!-- Render entire form automatically -->
<form method="POST">
  {% csrf_token %} {{ form.as_p }}
  <!-- Render as paragraphs -->
  <button type="submit">Submit</button>
</form>
```

Other rendering options:

- `{{ form.as_p }}` - Renders fields wrapped in `<p>` tags
- `{{ form.as_table }}` - Renders fields as table rows
- `{{ form.as_ul }}` - Renders fields as list items

---

#### Method 2: Manual Field Rendering

```html
<form method="POST">
  {% csrf_token %}

  <div class="form-group">
    <label for="{{ form.name.id_for_label }}">Name:</label>
    {{ form.name }} {% if form.name.errors %}
    <span class="error">{{ form.name.errors.0 }}</span>
    {% endif %}
  </div>

  <div class="form-group">
    <label for="{{ form.email.id_for_label }}">Email:</label>
    {{ form.email }} {% if form.email.errors %}
    <span class="error">{{ form.email.errors.0 }}</span>
    {% endif %}
  </div>

  <button type="submit">Submit</button>
</form>
```

---

#### Method 3: Loop Through Fields

```html
<form method="POST">
  {% csrf_token %} {% for field in form %}
  <div class="form-group">
    <label>{{ field.label }}</label>
    {{ field }} {% if field.help_text %}
    <small>{{ field.help_text }}</small>
    {% endif %} {% for error in field.errors %}
    <span class="error">{{ error }}</span>
    {% endfor %}
  </div>
  {% endfor %}

  <!-- Display non-field errors -->
  {% if form.non_field_errors %}
  <div class="errors">
    {% for error in form.non_field_errors %}
    <p>{{ error }}</p>
    {% endfor %}
  </div>
  {% endif %}

  <button type="submit">Submit</button>
</form>
```

---

### Complete Example

Here's a complete example showing model, form, view, and template:

**Model:**

```python
# models.py
from django.db import models


class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    isbn = models.CharField(max_length=13)
    published_date = models.DateField()
    price = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return self.title
```

**Form:**

```python
# forms.py
from django import forms
from .models import Book


class BookForm(forms.ModelForm):
    class Meta:
        model = Book
        fields = ['title', 'author', 'isbn', 'published_date', 'price']
        widgets = {
            'title': forms.TextInput(attrs={'class': 'form-control'}),
            'author': forms.TextInput(attrs={'class': 'form-control'}),
            'isbn': forms.TextInput(attrs={'class': 'form-control'}),
            'published_date': forms.DateInput(attrs={
                'class': 'form-control',
                'type': 'date'
            }),
            'price': forms.NumberInput(attrs={'class': 'form-control'}),
        }

    def clean_isbn(self):
        isbn = self.cleaned_data.get('isbn')
        if len(isbn) != 13:
            raise forms.ValidationError('ISBN must be exactly 13 characters')
        return isbn
```

**View:**

```python
# views.py
from django.shortcuts import render, redirect
from .forms import BookForm


def add_book(request):
    if request.method == 'POST':
        form = BookForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('book_list')
    else:
        form = BookForm()

    return render(request, 'add_book.html', {'form': form})
```

**Template:**

```html
<!-- templates/add_book.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Add Book</title>
    <style>
      .form-control {
        padding: 8px;
        width: 300px;
        margin-bottom: 10px;
      }
      .error {
        color: red;
        font-size: 0.9em;
      }
      label {
        display: block;
        margin-bottom: 5px;
        font-weight: bold;
      }
    </style>
  </head>
  <body>
    <h1>Add New Book</h1>

    <form method="POST">
      {% csrf_token %} {% for field in form %}
      <div>
        <label>{{ field.label }}</label>
        {{ field }} {% for error in field.errors %}
        <span class="error">{{ error }}</span>
        {% endfor %}
      </div>
      {% endfor %}

      <button type="submit">Add Book</button>
    </form>
  </body>
</html>
```

**URL:**

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('book/add/', views.add_book, name='add_book'),
]
```

---

### Summary Table

| Concept                  | Description                                          |
| ------------------------ | ---------------------------------------------------- |
| **forms.Form**           | Regular form not tied to any model                   |
| **forms.ModelForm**      | Form automatically generated from a model            |
| **cleaned_data**         | Dictionary containing validated form data            |
| **is_valid()**           | Method to check if form data passes all validations  |
| **clean\_<fieldname>()** | Custom validation method for a specific field        |
| **clean()**              | Method for cross-field validation                    |
| **save()**               | ModelForm method to save data to database            |
| **widgets**              | Control HTML rendering of form fields                |
| **{% csrf_token %}**     | Template tag for CSRF protection (required for POST) |

---
