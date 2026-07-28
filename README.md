# docker-django-project

A Django web application containerized with Docker — a basic "DevOps demo" app displaying an agenda page, packaged so it builds and runs the same way anywhere.

## Everyfile explanation

docker-django-project/

Dockerfile                          # builds the container image

requirements.txt                    # Python deps: Django, tzdata

devops/                             # Django project root

    manage.py                       # Django's CLI entry point
    
    devops/                         # project-level config
    
       ├── settings.py                 # app config: DEBUG=True, ALLOWED_HOSTS=["*"], INSTALLED_APPS
    
       ├── urls.py                     # root URL routing — maps "/" to the home view
    
       ├── wsgi.py / asgi.py           # entry points for production servers (unused here; dev server is used instead)
    
       └── __init__.py
    
    ── demo/                           # the actual Django "app"
    
        ├── apps.py                     # registers the app as "demo"
        
        ├── models.py                   # empty — no database models defined
        
        ├── views.py                    # defines the `home` view, renders demo_site.html
        
        ├── urls.py                     # a leftover/unused urls file (not wired into the project — devops/urls.py is what's actually
        used)
        
        ├── admin.py / tests.py         # untouched Django defaults
        
        └── templates/demo/demo_site.html  # the HTML page that's actually shown to visitors


## Step-by-step: how it all connects

1. **`requirements.txt`** lists what needs installing: `Django` and `tzdata`.
2. **`Dockerfile`** builds the image:
   - Starts from plain `ubuntu`, copies in `requirements.txt` and the `devops/` project folder.
   - Installs `python3`, `pip`, and `venv`.
   - Creates a virtual environment (`venv1`) and installs the Python dependencies inside it.
   - On container start, activates the venv and runs `python3 manage.py runserver 0.0.0.0:8000`.
3. **`manage.py`** is Django's command-line tool — it's what actually launches the dev server, using `devops.settings` as the configuration module.
4. **`devops/settings.py`** configures the app: debug mode is on, all hosts are allowed (`ALLOWED_HOSTS = ["*"]` — fine for local testing, not for production), and the `demo` app isn't even required to be in `INSTALLED_APPS` since it has no models or migrations of its own.
5. **`devops/urls.py`** (the root URL config) imports `home` directly from `demo/views.py` and maps the site's root path `/` to it. This is the file Django actually uses for routing.
6. **`demo/views.py`** defines `home(request)`, which renders `demo/templates/demo/demo_site.html`.
7. **`demo_site.html`** is a static HTML/CSS page — a simple header/nav/article/footer layout — that's returned to the browser when you visit the site.

##  Requirements

- Docker installed — no local Python/Django install needed, that's the point of containerizing it

##  How to run

# 1. Build the image
docker build -t docker-django-project .

# 2. Run the container, mapping container port 8000 to your machine's port 8000
docker run -p 8000:8000 docker-django-project

# 3. Open in a browser
# http://localhost:8000

You should see the "Django Application demo" agenda page.
