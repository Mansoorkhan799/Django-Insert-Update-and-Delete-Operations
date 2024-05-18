# CURD-INSERT-UPDATE-DELETE-OPERATIONS-DJANGO
Building a Django Insert, Update, and Delete in Visual Studio
Steps to Successfully run the code on your visual Studio.
1- Download the zip file
2- Extract the folder.
3- Open VS-Code and open the extracted folder.
3.1: Open cmd and type 'python manage.py runserver'

If you want to start from scratch:

1- Now Open the Command Prompt.
2- Type:
.Pip install Django
.django-admin startproject myproject
.cd myproject
.python manage.py startapp myapp

A folder named as myproject will be created and you have to open that folder in vs-code.
Now:
1- Edit myapp/models.py to define a simple model:
from django.db import models

class Item(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
2- Edit myapp/admin.py to register the model with the admin site:
from django.contrib import admin
from .models import Item

admin.site.register(Item)

3- Edit myproject/settings.py to add the app to INSTALLED_APPS:
add:  'myapp', in the INSTALLED_APPS.

4- Run the following migrations in cmd:
python manage.py makemigrations
python manage.py migrate

Now in vs code:
5- Edit myapp/views.py to add the following views:

from django.shortcuts import render, redirect, get_object_or_404
from .models import Item
from .forms import ItemForm

def item_list(request):
    items = Item.objects.all()
    return render(request, 'myapp/item_list.html', {'items': items})

def item_create(request):
    if request.method == 'POST':
        form = ItemForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('item_list')
    else:
        form = ItemForm()
    return render(request, 'myapp/item_form.html', {'form': form})

def item_update(request, id):
    item = get_object_or_404(Item, id=id)
    if request.method == 'POST':
        form = ItemForm(request.POST, instance=item)
        if form.is_valid():
            form.save()
            return redirect('item_list')
    else:
        form = ItemForm(instance=item)
    return render(request, 'myapp/item_form.html', {'form': form})

def item_delete(request, id):
    item = get_object_or_404(Item, id=id)
    if request.method == 'POST':
        item.delete()
        return redirect('item_list')
    return render(request, 'myapp/item_confirm_delete.html', {'item': item})

6- Edit myapp/forms.py to create forms for the Item model:

from django import forms
from .models import Item

class ItemForm(forms.ModelForm):
    class Meta:
        model = Item
        fields = ['name', 'description']

7- Edit myapp/urls.py to define URL patterns for the views:

from django.urls import path
from . import views

urlpatterns = [
    path('', views.item_list, name='item_list'),
    path('item/new/', views.item_create, name='item_create'),
    path('item/<int:id>/edit/', views.item_update, name='item_update'),
    path('item/<int:id>/delete/', views.item_delete, name='item_delete'),
]

8- Include myapp URLs in the project's main urls.py:

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls')),
]

9-Create a Directory for Templates so:
 9.1: Create myapp/templates/myapp/item_list.html:
 <!DOCTYPE html>
<html>
<head>
    <title>Items</title>
</head>
<body>
    <h1>Items</h1>
    <a href="{% url 'item_create' %}">Add New Item</a>
    <ul>
        {% for item in items %}
            <li>
                {{ item.name }} - {{ item.description }}
                <a href="{% url 'item_update' item.id %}">Edit</a>
                <form action="{% url 'item_delete' item.id %}" method="post" style="display:inline;">
                    {% csrf_token %}
                    <button type="submit">Delete</button>
                </form>
            </li>
        {% endfor %}
    </ul>
</body>
</html>

 9.2: Create myapp/templates/myapp/item_form.html:
<!DOCTYPE html>
<html>
<head>
    <title>Item Form</title>
</head>
<body>
    <h1>Item Form</h1>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Save</button>
    </form>
    <a href="{% url 'item_list' %}">Back to list</a>
</body>
</html>

9.3: Create myapp/templates/myapp/item_confirm_delete.html:
<!DOCTYPE html>
<html>
<head>
    <title>Delete Item</title>
</head>
<body>
    <h1>Are you sure you want to delete "{{ item.name }}"?</h1>
    <form method="post">
        {% csrf_token %}
        <button type="submit">Confirm</button>
    </form>
    <a href="{% url 'item_list' %}">Cancel</a>
</body>
</html>

10- Make sure Django knows where to find your templates. Your app structure should look like this:

myproject/
    manage.py
    myproject/
        settings.py
        urls.py
        ...
    myapp/
        models.py
        views.py
        forms.py
        urls.py
        templates/
            myapp/
                item_list.html
                item_form.html
                item_confirm_delete.html
        ...

11- Ensure your views in myapp/views.py are set up to use these templates:

from django.shortcuts import render, redirect, get_object_or_404
from .models import Item
from .forms import ItemForm

def item_list(request):
    items = Item.objects.all()
    return render(request, 'myapp/item_list.html', {'items': items})

def item_create(request):
    if request.method == 'POST':
        form = ItemForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('item_list')
    else:
        form = ItemForm()
    return render(request, 'myapp/item_form.html', {'form': form})

def item_update(request, id):
    item = get_object_or_404(Item, id=id)
    if request.method == 'POST':
        form = ItemForm(request.POST, instance=item)
        if form.is_valid():
            form.save()
            return redirect('item_list')
    else:
        form = ItemForm(instance=item)
    return render(request, 'myapp/item_form.html', {'form': form})

def item_delete(request, id):
    item = get_object_or_404(Item, id=id)
    if request.method == 'POST':
        item.delete()
        return redirect('item_list')
    return render(request, 'myapp/item_confirm_delete.html', {'item': item})

12- Finally run this command in cmd:
python manage.py runserver.
Open the provided link on the web(Google).




 


