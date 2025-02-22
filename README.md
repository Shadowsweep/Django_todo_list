# Django To-Do List App

This is a simple Django-based To-Do List application that allows users to add and delete tasks.

## Setup Instructions

### Step 1: Set Up Django Project

Open the terminal and create a new Django project:

```sh
django-admin startproject todo_project
cd todo_project
```

Create a Django app:

```sh
python manage.py startapp tasks
```

Add `tasks` to `INSTALLED_APPS` in `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'tasks',  # Add this line
]
```

### Step 2: Create the Task Model

Edit `tasks/models.py`:

```python
from django.db import models

class Task(models.Model):
    title = models.CharField(max_length=255)
    completed = models.BooleanField(default=False)

    def __str__(self):
        return self.title
```

Run migrations:

```sh
python manage.py makemigrations
python manage.py migrate
```

### Step 3: Create Forms

Edit `tasks/forms.py`:

```python
from django import forms
from .models import Task

class TaskForm(forms.ModelForm):
    class Meta:
        model = Task
        fields = ['title', 'completed']
```

### Step 4: Create Views

Edit `tasks/views.py`:

```python
from django.shortcuts import render, redirect
from .models import Task
from .forms import TaskForm

def task_list(request):
    tasks = Task.objects.all()
    form = TaskForm()
    
    if request.method == "POST":
        form = TaskForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('task_list')

    return render(request, 'tasks/task_list.html', {'tasks': tasks, 'form': form})

def delete_task(request, task_id):
    task = Task.objects.get(id=task_id)
    task.delete()
    return redirect('task_list')
```

### Step 5: Configure URLs

Edit `tasks/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.task_list, name='task_list'),
    path('delete/<int:task_id>/', views.delete_task, name='delete_task'),
]
```

Include `tasks.urls` in `todo_project/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('tasks.urls')),
]
```

### Step 6: Create Templates

Create `tasks/templates/tasks/task_list.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>To-Do List</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            text-align: center;
            padding: 20px;
        }

        h1 {
            color: #333;
        }

        form {
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            display: inline-block;
            margin-bottom: 20px;
        }

        button {
            background: #28a745;
            color: white;
            border: none;
            padding: 10px 15px;
            cursor: pointer;
            border-radius: 5px;
            font-size: 16px;
        }

        button:hover {
            background: #218838;
        }

        ul {
            list-style: none;
            padding: 0;
        }

        li {
            background: white;
            margin: 10px auto;
            padding: 10px;
            width: 50%;
            border-radius: 5px;
            box-shadow: 0px 0px 5px rgba(0, 0, 0, 0.1);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        a {
            text-decoration: none;
            background: #dc3545;
            color: white;
            padding: 5px 10px;
            border-radius: 5px;
        }

        a:hover {
            background: #c82333;
        }
    </style>
</head>
<body>
    <h1>To-Do List</h1>
    <form method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Add Task</button>
    </form>

    <ul>
        {% for task in tasks %}
            <li>
                <span>{{ task.title }} - {% if task.completed %} ✅ {% else %} ❌ {% endif %}</span>
                <a href="{% url 'delete_task' task.id %}">Delete</a>
            </li>
        {% endfor %}
    </ul>
</body>
</html>
```

### Step 7: Create a Superuser

Run the following command in your terminal:

```sh
python manage.py createsuperuser
```

You'll be prompted to enter:
- **Username**
- **Email** (optional)
- **Password**

Once completed, the superuser will be created.

### Step 8: Register Task Model in Django Admin

Edit `tasks/admin.py`:

```python
from django.contrib import admin
from .models import Task

admin.site.register(Task)
```

### Step 9: Run the Server

```sh
python manage.py runserver
```

Visit: [http://127.0.0.1:8000/admin](http://127.0.0.1:8000/admin) to log in using your superuser credentials and manage tasks from the Django admin panel.

---

Your Django To-Do List app is now fully functional! 🚀

