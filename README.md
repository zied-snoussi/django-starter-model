# Django Event Management System

A Django web application for managing events and participants with user authentication and participation tracking.

## 📋 Table of Contents
- [Project Overview](#project-overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Models Documentation](#models-documentation)
- [Views and URLs](#views-and-urls)
- [Templates](#templates)
- [Installation & Setup](#installation--setup)
- [Running the Project](#running-the-project)
- [Database Migrations](#database-migrations)
- [Creating New Models](#creating-new-models)
- [Creating New Templates](#creating-new-templates)
- [Common Commands](#common-commands)

## 🎯 Project Overview

This Django application manages events with the following core functionality:
- User management with custom Person model
- Event creation and management
- Event participation tracking
- Category-based event organization
- Image upload for events

## ✨ Features

### 🎪 Event Management
- **Create Events**: Users can create events with title, description, category, image, and date
- **Event Categories**: Support for sport, music, and cinema events
- **Event Status**: Active/inactive event states
- **Event Images**: Image upload functionality for events
- **Date Validation**: Events must be scheduled for future dates

### 👥 User Management
- **Custom User Model**: Extended AbstractUser with CIN and email validation
- **Email Validation**: Users must have @esprit.tn email addresses
- **CIN Validation**: 8-character CIN requirement
- **User Authentication**: Login/logout functionality

### 🎫 Participation System
- **Event Participation**: Users can join events
- **Participation Tracking**: Track when users joined events
- **Unique Participation**: Prevents duplicate participation
- **Participation Cancellation**: Users can cancel their participation

### 🎨 Web Interface
- **Event Listing**: View all active events
- **Event Details**: Detailed event information and participation status
- **CRUD Operations**: Create, Read, Update, Delete events
- **Responsive Design**: Bootstrap-based templates

## 📁 Project Structure

```
django-starter-model/
├── manage.py                    # Django management script
├── db.sqlite3                  # SQLite database file
├── media/                      # User uploaded files
│   └── images/                 # Event images
├── firstProject/               # Main project settings
│   ├── settings.py            # Django settings
│   ├── urls.py                # Main URL configuration
│   ├── wsgi.py                # WSGI configuration
│   └── asgi.py                # ASGI configuration
├── Event/                      # Event app
│   ├── models.py              # Event and Participants models
│   ├── views.py               # Event views (function and class-based)
│   ├── forms.py               # Event forms
│   ├── urls.py                # Event URL patterns
│   ├── admin.py               # Admin configuration
│   └── migrations/            # Database migrations
├── Person/                     # Person app
│   ├── models.py              # Custom User model
│   ├── views.py               # Person views
│   ├── admin.py               # Admin configuration
│   └── migrations/            # Database migrations
└── Template/                   # HTML templates
    ├── base.html              # Base template
    └── event/                 # Event templates
        ├── list.html          # Event list template
        ├── details.html       # Event details template
        ├── add.html           # Add event template
        ├── update.html        # Update event template
        └── delete.html        # Delete event template
```

## 📊 Models Documentation

### Person Model (Custom User)
```python
class Person(AbstractUser):
    cin = models.CharField(primary_key=True, max_length=8, validators=[validate_cin])
    username = models.CharField(max_length=20, unique=True)
    email = models.EmailField(max_length=30, validators=[validate_email])
```

**Features:**
- Custom primary key using CIN (8 characters)
- Email validation for @esprit.tn domain
- Extends Django's AbstractUser for authentication

### Event Model
```python
class Event(models.Model):
    title = models.CharField(max_length=30)
    description = models.TextField()
    category = models.CharField(choices=category_list, max_length=20)
    image = models.ImageField(null=True, upload_to='images')
    state = models.BooleanField(default=True)
    nbr_participants = models.IntegerField(null=True)
    evt_date = models.DateTimeField()
    creation_date = models.DateTimeField(auto_now_add=True)
    updated_date = models.DateTimeField(auto_now=True)
    organisateur = models.ForeignKey(Person, on_delete=models.SET_NULL, null=True)
    participant = models.ManyToManyField(Person, through="Participants", related_name="participant")
```

**Features:**
- Three categories: sport, music, cinema
- Image upload with automatic file management
- Automatic timestamp tracking
- Foreign key relationship with organizer
- Many-to-many relationship with participants through intermediate model
- Date validation constraint (events must be in the future)

### Participants Model (Intermediate Model)
```python
class Participants(models.Model):
    event = models.ForeignKey(Event, on_delete=models.CASCADE)
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    participation_date = models.DateField(auto_now_add=True)
```

**Features:**
- Tracks event participation
- Automatic participation date recording
- Unique constraint preventing duplicate participation

## 🌐 Views and URLs

### Function-Based Views
- `hello()` - Welcome page
- `listEvent()` - Display active events
- `details(ide)` - Event details with participation status
- `delete(idEvent)` - Delete specific event
- `participer(ide)` - Join an event

### Class-Based Views
- `List(ListView)` - Event listing using generic view
- `DetailsEvent(DetailView)` - Event details using generic view
- `DeleteEvent(DeleteView)` - Event deletion using generic view
- `AddEvent(CreateView)` - Add new event
- `UpdateEvent(UpdateView)` - Update existing event

### URL Patterns
```python
urlpatterns = [
    path('hi/', hello),
    path('list/', listEvent, name="list"),
    path('details/<int:ide>', details, name="detailsEvent"),
    path('add/', AddEvent.as_view(), name="addEvent"),
    path('update/<int:pk>', UpdateEvent.as_view(), name="updateEvent"),
    path('delete/<int:idEvent>', delete, name="deleteEvent"),
    path('participer/<int:ide>', participer, name="participe"),
]
```

## 🎨 Templates

### Base Template (`base.html`)
- Navigation menu with Add Event and List Events links
- User authentication status display
- Bootstrap framework integration

### Event Templates
- `list.html` - Displays all active events in a grid/list format
- `details.html` - Shows event details with participation button
- `add.html` - Form for creating new events
- `update.html` - Form for editing existing events
- `delete.html` - Confirmation page for event deletion

## 🚀 Installation & Setup

### Prerequisites
```bash
# Ensure you have Python 3.7+ installed
python --version

# Install pip if not already installed
python -m ensurepip --upgrade
```

### 1. Clone the Repository
```bash
git clone https://github.com/zied-snoussi/django-starter-model.git
cd django-starter-model
```

### 2. Create Virtual Environment
```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate
```

### 3. Install Dependencies
```bash
# Install Django and required packages
pip install django
pip install Pillow  # For image handling

# Or create requirements.txt and install
pip freeze > requirements.txt
pip install -r requirements.txt
```

### 4. Configure Settings
Check `firstProject/settings.py` for:
- Database configuration (SQLite by default)
- Media files configuration
- Installed apps configuration

## 🏃‍♂️ Running the Project

### 1. Apply Database Migrations
```bash
# Navigate to project directory
cd django-starter-model

# Apply existing migrations
python manage.py migrate

# Create superuser (optional)
python manage.py createsuperuser
```

### 2. Start Development Server
```bash
# Run the development server
python manage.py runserver

# Server will start at http://127.0.0.1:8000/
# Access admin panel at http://127.0.0.1:8000/admin/
```

### 3. Access the Application
- **Event List**: `http://127.0.0.1:8000/list/`
- **Add Event**: `http://127.0.0.1:8000/add/`
- **Admin Panel**: `http://127.0.0.1:8000/admin/`

## 🗄️ Database Migrations

### Understanding Migrations
Migrations are Django's way of tracking changes to your models and applying them to your database.

### Common Migration Commands
```bash
# Check migration status
python manage.py showmigrations

# Create new migrations after model changes
python manage.py makemigrations

# Apply migrations to database
python manage.py migrate

# Create migrations for specific app
python manage.py makemigrations Event
python manage.py makemigrations Person

# Show SQL commands for migrations (without applying)
python manage.py sqlmigrate Event 0001

# Reset migrations (careful - this can cause data loss)
python manage.py migrate Event zero
```

### Migration Workflow
1. **Modify models** in `models.py`
2. **Create migrations**: `python manage.py makemigrations`
3. **Review migrations** in the `migrations/` folder
4. **Apply migrations**: `python manage.py migrate`

## 🆕 Creating New Models

### Step-by-Step Process

#### 1. Define the Model
Create or modify models in your app's `models.py`:

```python
# Example: Creating a new Comment model
from django.db import models
from Event.models import Event
from Person.models import Person

class Comment(models.Model):
    event = models.ForeignKey(Event, on_delete=models.CASCADE, related_name='comments')
    author = models.ForeignKey(Person, on_delete=models.CASCADE)
    content = models.TextField(max_length=500)
    created_date = models.DateTimeField(auto_now_add=True)
    is_approved = models.BooleanField(default=False)
    
    class Meta:
        ordering = ['-created_date']
        verbose_name = "Comment"
        verbose_name_plural = "Comments"
    
    def __str__(self):
        return f"Comment by {self.author.username} on {self.event.title}"
```

#### 2. Create and Apply Migrations
```bash
# Create migrations for the new model
python manage.py makemigrations

# Apply migrations
python manage.py migrate
```

#### 3. Register in Admin (Optional)
Add to `admin.py`:

```python
from django.contrib import admin
from .models import Comment

@admin.register(Comment)
class CommentAdmin(admin.ModelAdmin):
    list_display = ['event', 'author', 'created_date', 'is_approved']
    list_filter = ['is_approved', 'created_date']
    search_fields = ['content', 'author__username']
```

#### 4. Create Forms (If Needed)
Add to `forms.py`:

```python
from django import forms
from .models import Comment

class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['content']
        widgets = {
            'content': forms.Textarea(attrs={'rows': 4, 'placeholder': 'Write your comment...'})
        }
```

## 🎨 Creating New Templates

### Template Structure Guidelines

#### 1. Create Template Directory
```bash
# Create new template directory for your app
mkdir Template/comment
```

#### 2. Base Template Extension
Create templates that extend `base.html`:

```html
<!-- Template/comment/list.html -->
{% extends 'base.html' %}

{% block title %}Comments{% endblock %}

{% block body %}
<div class="container">
    <h2>Event Comments</h2>
    
    {% for comment in comments %}
    <div class="card mb-3">
        <div class="card-body">
            <h5 class="card-title">{{ comment.author.username }}</h5>
            <p class="card-text">{{ comment.content }}</p>
            <small class="text-muted">{{ comment.created_date }}</small>
        </div>
    </div>
    {% empty %}
    <p>No comments yet.</p>
    {% endfor %}
</div>
{% endblock %}
```

#### 3. Form Templates
```html
<!-- Template/comment/add.html -->
{% extends 'base.html' %}

{% block title %}Add Comment{% endblock %}

{% block body %}
<div class="container">
    <h2>Add Comment</h2>
    
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit" class="btn btn-primary">Submit Comment</button>
        <a href="{% url 'event:list' %}" class="btn btn-secondary">Cancel</a>
    </form>
</div>
{% endblock %}
```

#### 4. Update Settings (If Needed)
Ensure your template directory is in `settings.py`:

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'Template'],  # Add your template directory
        'APP_DIRS': True,
        # ... other settings
    },
]
```

## 📝 Common Commands

### Development Commands
```bash
# Start development server
python manage.py runserver

# Start server on specific port
python manage.py runserver 8080

# Start server accessible from other devices
python manage.py runserver 0.0.0.0:8000
```

### Database Commands
```bash
# Create superuser
python manage.py createsuperuser

# Open Django shell
python manage.py shell

# Dump data to JSON
python manage.py dumpdata > backup.json

# Load data from JSON
python manage.py loaddata backup.json

# Clear database (careful!)
python manage.py flush
```

### Migration Commands
```bash
# Check for model changes without creating migrations
python manage.py makemigrations --dry-run

# Create empty migration file
python manage.py makemigrations --empty Event

# Reverse specific migration
python manage.py migrate Event 0001

# Show migration plan
python manage.py migrate --plan
```

### Static Files Commands
```bash
# Collect static files (for production)
python manage.py collectstatic

# Find static files
python manage.py findstatic admin/css/base.css
```

### Testing Commands
```bash
# Run all tests
python manage.py test

# Run tests for specific app
python manage.py test Event

# Run specific test
python manage.py test Event.tests.EventModelTest
```

### Debugging Commands
```bash
# Check project for common issues
python manage.py check

# Check specific app
python manage.py check Event

# Show SQL queries for operations
python manage.py shell
>>> from django.db import connection
>>> connection.queries
```

## 🔧 Environment Setup

### Creating requirements.txt
```bash
# Generate requirements file
pip freeze > requirements.txt

# Install from requirements
pip install -r requirements.txt
```

### Example requirements.txt
```txt
Django==4.2
Pillow==10.0.0
```

## 🚀 Deployment Notes

### Production Settings
- Set `DEBUG = False`
- Configure `ALLOWED_HOSTS`
- Use environment variables for sensitive data
- Set up proper database (PostgreSQL/MySQL)
- Configure static file serving

### Security Checklist
```bash
# Check deployment readiness
python manage.py check --deploy
```

## 📚 Additional Resources

- [Django Documentation](https://docs.djangoproject.com/)
- [Django Best Practices](https://django-best-practices.readthedocs.io/)
- [Django Tutorial](https://docs.djangoproject.com/en/stable/intro/tutorial01/)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Write tests
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License.

---

**Happy Coding! 🎉**

*Last updated: {{ current_date }}*
