# django-heroku
Minimal configuration to host a Django project on Heroku

## Create the project directory
* `mkdir directory_name`
* `cd directory_name`

## Create and activate your virtualenv
* `python -m venv venv`
* for windows: `.\venv\Scripts\activate`
* for linux: `source venv/bin/activate`

## Installing Django
* `pip install django`

## Creating the Django project
* `django-admin startproject myproject`

## Creating the Git repository
* `git init`
* Create a file called `.gitignore` with the following content:
```
# See the name for your IDE
.idea
# If you are using sqlite3
*.sqlite3
# Name of your venv
.venv
*pyc
```
* `git add .`
* `git commit -m "Initial commit"`

## Hiding instance configuration
* `pip install python-decouple`
* Create an .env file at the root path and insert the following variables:
  - SECRET_KEY=Your$eCretKeyHere (Get this secret key from settings.py)
  - DEBUG=True

### settings.py
* from decouple import config
* SECRET_KEY = config('SECRET_KEY')
* DEBUG = config('DEBUG', default=False, cast=bool)

## Configuring the Database (You don't need that if you already have a database).
* `pip install dj-database-url`

### Settings.py
* from dj_database_url import parse as dburl
* default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')
* DATABASES = {'default': config('DATABASE_URL', default=default_dburl, cast=dburl),}


## Static files 
`pip install dj-static`

### wsgi.py
* from dj_static import Cling
* application = Cling(get_wsgi_application())
* Also don't forget to check "DJANGO_SETTINGS_MODULE". It is prone to frequent mistakes.

### settings.py
* STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

## Create a requirements-dev.txt
`pip freeze > requirements-dev.txt`

## Create a requirements.txt file, include a reference to the previous file and add two more requirements
* -r requirements-dev.txt
* gunicorn
* psycopg2

## Create a file Procfile and add the following code
* web: gunicorn myproject.wsgi
* You can check Django or Heroku websites for more information:
  - https://docs.djangoproject.com/en/2.2/howto/deployment/wsgi/gunicorn/
  - https://devcenter.heroku.com/articles/django-app-configuration

## Creating the app on Heroku
You should install Heroku CLI tools on your computer (See https://devcenter.heroku.com/articles/heroku-cli) 
* heroku apps:create app-name (you can create it on Heroku itself if you want)
* You can also login to Heroku with: heroku login

## Setting allowed hosts
* include your address at the ALLOWED_HOSTS directive in settings.py - just the domain, make sure that you take the protocol and slashes out of the string

## Heroku install config plugin
* heroku plugins:install heroku-config

### Sending configs from .env to Heroku (You have to be inside the folder where .env file is)
* heroku plugins:install heroku-config
* heroku config:push -a

### To show heroku configs
* heroku config 
  - (check this; if you fail to change it by code, try changing it on the Heroku dashboard)

## Publishing the app
```
git add .
git commit -m "Configuring the app"
git push heroku master
```

## Creating the Database
* `heroku run python manage.py migrate`

## Creating the Django admin user
* `heroku run python manage.py createsuperuser`

## EXTRAS
### You may need to disable the collectstatic
* `heroku config:set DISABLE_COLLECTSTATIC=1`

### It's also recommended to set this configuration in your Heroku settings
* WEB_CONCURRENCY = 3

### Changing a specific configuration
* `heroku config:set DEBUG=True`

---
