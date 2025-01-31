# Django To-Do List App

## Overview
A simple To-Do List application built with Django, allowing users to add and delete tasks.

## Features
- Add tasks with a title
- Mark tasks as completed
- Delete tasks from the list

## Installation

### 1. Set Up Django Project
Open terminal and create a new Django project:
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

### 2. Create the Task Model
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

### 3. Create Forms
Edit `tasks/forms.py`:
```python
from django import forms
from .models import Task

class TaskForm(forms.ModelForm):
    class Meta:
        model = Task
        fields = ['title', 'completed']
```

### 4. Create Views
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

### 5. Configure URLs
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

### 6. Create Templates
Create `tasks/templates/tasks/task_list.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>To-Do List</title>
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
                {{ task.title }} - 
                {% if task.completed %} ✅ {% else %} ❌ {% endif %}
                <a href="{% url 'delete_task' task.id %}">Delete</a>
            </li>
        {% endfor %}
    </ul>
</body>
</html>
```

### 7. Run the Server
```sh
python manage.py runserver
```
Visit: **http://127.0.0.1:8000/**

## License
This project is open-source under the MIT License.

