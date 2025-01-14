# M7-L9-Crud_V2
Educativo y de Aprendizaje Personal# M7-L9-Crud_V2_Bootstrap 4
---
## Tabla de Contenidos
- [Tecnologías](#Tecnologías)
- [Configuración Inicial](#configuración-Inicial)
- [Creación del Modelo](#creación-del-modelo)
---
# Tecnologías
- Django: Framework web en Python.
- SQLite:
--- 
# Configuración Inicial 
1. Entorno virtual 
    ```bash 
    python -m venv venv

2. Activar el entorno virtual
    ```bash 
    venv\Scripts\activate

3. Instalar Django
    ```bash 
    pip install django 
        
4. Actualizamos el pip 
    ```bash
    python.exe -m pip install --upgrade pip

5. Guardamos dependencias
    ```bash
    pip freeze > requirements.txt

6. Crear el proyecto de django  link
    ```bash 
    django-admin startproject link

7. Ingresamos al linkdump
    ```bash 
    cd linkdump

9. Creamos la aplicacion llamada app
    ```bash     
    python manage.py startapp app


10. Configuración de /settings.py 
    ```bash 
        INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'app',
    ]

# Creación del Modelo 

11. app/models.py
    ```bash
    from django.db import models

    class Link(models.Model):
        url = models.URLField(max_length=200)
        description = models.TextField()

        def __str__(self):
            return self.url

12. Creamos el .forms en app
    ```bash
    from django import forms
    from .models import Link

    class LinkForm(forms.ModelForm):
        class Meta:
            model = Link
            fields = ['url', 'description']

13. Creamos las migraciones
    ```bash
    python manage.py migrate
    python manage.py makemigrations
    python manage.py migrate

14. en app/views.py creamos las vistas
    ```bash
    from django.shortcuts import render, get_object_or_404, redirect
    from .models import Link
    from .forms import LinkForm

    def link_list(request):
        links = Link.objects.all()
        return render(request, 'app/link_list.html', {'links': links})

    def link_create(request):
        if request.method == "POST":
            form = LinkForm(request.POST)
            if form.is_valid():
                form.save()
                return redirect('link_list')
        else:
            form = LinkForm()
        return render(request, 'app/link_form.html', {'form': form})

    def link_update(request, pk):
        link = get_object_or_404(Link, pk=pk)
        if request.method == "POST":
            form = LinkForm(request.POST, instance=link)
            if form.is_valid():
                form.save()
                return redirect('link_list')
        else:
            form = LinkForm(instance=link)
        return render(request, 'app/link_form.html', {'form': form})

    def link_delete(request, pk):
        link = get_object_or_404(Link, pk=pk)
        if request.method == "POST":
            link.delete()
            return redirect('link_list')
        return render(request, 'app/link_confirm_delete.html', {'link': link})
        
15. Creamos los templates/app/link_list.html
    ```bash
    {% extends 'base.html' %}

    {% block content %}
    <div class="container mt-5">
        <h2 class="mb-4">Links</h2>
        <div class="d-flex justify-content-end mb-3">
            <a href="{% url 'link_create' %}" class="btn btn-success">Add New Link</a>
        </div>
        <ul class="list-group">
            {% for link in links %}
            <li class="list-group-item d-flex justify-content-between align-items-center">
                <div>
                    <a href="{{ link.url }}" target="_blank" class="text-primary">{{ link.url }}</a>
                    <span class="text-muted">- {{ link.description }}</span>
                </div>
                <div>
                    <a href="{% url 'link_update' link.pk %}" class="btn btn-sm btn-warning">Edit</a>
                    <a href="{% url 'link_delete' link.pk %}" class="btn btn-sm btn-danger">Delete</a>
                </div>
            </li>
            {% empty %}
            <li class="list-group-item text-center text-muted">
                No links available.
            </li>
            {% endfor %}
        </ul>
    </div>
{% endblock %}
16. Creamos los templates/app/link_form.html
    ```bash
   {% extends 'base.html' %}

    {% block content %}
    <div class="container mt-5">
        <div class="card shadow-sm">
            <div class="card-body">
                <h2 class="card-title">
                    {% if form.instance.pk %}Edit{% else %}New{% endif %} Link
                </h2>
                <form method="post" class="mt-4">
                    {% csrf_token %}
                    <div class="mb-3">
                        {{ form.as_p }}
                    </div>
                    <button type="submit" class="btn btn-primary">Save</button>
                    <a href="{% url 'link_list' %}" class="btn btn-secondary">Cancel</a>
                </form>
            </div>
        </div>
    </div>
    {% endblock %}

17. Genero los templates/app/link_confirm_delete.html
    ```bash
    {% extends 'base.html' %}

    {% block content %}
    <div class="container mt-5">
        <div class="card shadow-sm">
            <div class="card-body">
                <h2 class="card-title text-danger">Delete Link</h2>
                <p class="card-text">Are you sure you want to delete <strong>"{{ link.url }}"</strong>?</p>
                <form method="post" class="mt-4">
                    {% csrf_token %}
                    <div class="d-flex gap-3">
                        <button type="submit" class="btn btn-danger">Confirm</button>
                        <a href="{% url 'link_list' %}" class="btn btn-secondary">Cancel</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
    {% endblock %}

18. Creamos los urls en app/urls.py
    ```bash
    from django.urls import path
    from . import views

    urlpatterns = [
        path('', views.link_list, name='link_list'),
        path('link/new/', views.link_create, name='link_create'),
        path('link/<int:pk>/edit/', views.link_update, name='link_update'),
        path('link/<int:pk>/delete/', views.link_delete, name='link_delete'),
    ]

19. Agregamos la urls en linkdunp/urls.py
    ```bash
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('app.urls')),
    ]

20. Buscamos el Archivo con data.py
    ```bash
    import os
    import django

    # Configura el entorno de Django
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'link.settings')
    django.setup()

    from app.models import Link

    # Datos de ejemplo para poblar las tablas
    links_data = [
        {"url": "https://www.djangoproject.com/", "description": "El sitio oficial de Django"},
        {"url": "https://docs.djangoproject.com/en/stable/", "description": "Documentación oficial de Django"},
        {"url": "https://www.djangoproject.com/community/", "description": "Comunidad de Django"},
    ]

    # Poblar la base de datos
    for link in links_data:
        Link.objects.create(url=link['url'], description=link['description'])

    print("Datos insertados exitosamente")

21. Ejecutamos el archivo para poblar con data.py
    ```bash
    python data.py

22. Configuramos los templates 
    ```bash	
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [BASE_DIR / 'templates'],
            'APP_DIRS': True,
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
23. Creamos el base.html
    ```bash
    {% load static %}
    {% load bootstrap4 %}
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
        <link rel="stylesheet" href="{% static 'css/reset.css' %}">
        {% bootstrap_css %}
    </head>

    <body>
        {% block content %}
        {% endblock %}
        {% bootstrap_javascript %}
    </body>

    </html>

24. Confiramos el static en link 
    ```bash
    STATICFILES_DIRS = [
        BASE_DIR / "static",  # Reemplaza "BASE_DIR" con la referencia a tu directorio base
    ]
25. Se corre el servidor y se revisa las urls http://127.0.0.1:8000/
    ```bash
    python manage.py runserver