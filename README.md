Tutorial: https://www.codementor.io/jamesezechukwu/how-to-deploy-django-app-on-heroku-dtsee04d4

Setup Django Project

```
$ virtualenv django_env

$ source django_env/scripts/activate

(django_env):~$ pip install django

(django_env):~$ django-admin.py startproject djangoherokuapp

djangoherokuapp
  |-- djangoherokuapp/
    |	  |---  __init_-.py
    |     |---  settings.py
    |     |---  urls.py
    |     |---  wsgi.py
    |-- manage.py

(django_env):~/Desktop$  cd djangoherokuapp
(django_env):~/Desktop/djangoherokuapp$  python manage.py startapp herokuapp

djangoherokuapp
  |-- djangoherokuapp/
    |	  |---  __init_-.py
    |     |---  settings.py
    |     |---  urls.py
    |     |---  wsgi.py
    |----- manage.py
    |----- herokuapp/
    |     |---  admin.py
    |     |---  apps.py
    |     |---  __init__.py
    |     |---  models.py
    |     |---  tests.py
    |     |---  views.py


Add 'herokuapp' to installed apps in settings.py

    INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'herokuapp',
]


Migrate the database.

(django_env):~/Desktop/djangoherokuapp$  python manage.py migrate


Start the development server.

(django_env):~/Desktop/djangoherokuapp$ python manage.py runserver

Visit http://127.0.0.1:8000/ on the browser. If your setup is correct, you should see the Django welcome page.

```

Deploy App to Heroku:

1. Log in to Heroku CLI

```
(django_env):~/Desktop/djangoherokuapp$ heroku login
```
2. Add a Procfile in the project root directory to define process types and explicitly declare what command should be executed to start your app.
```
(django_env):~/Desktop/djangoherokuapp$ touch Procfile
```

3. Open the Procfile and add the line below.
```
web: gunicorn djangoherokuapp.wsgi --log-file -
```
4. Add a runtime.txt file in the project root directory and specify the correct Python version.
```
python-3.7.4
```

5. Install the following packages in the environment.
```
(django_env):~/Desktop/djangoherokuapp$ pip install gunicorn dj-database-url whitenoise psycopg2

```

6. Add a requirements.txt file
```
(django_env):~/Desktop/djangoherokuapp$ pip freeze > requirements.txt
```

7. Set Up the Static Assets
   
Open up settings.py file and make the following changes, preferably at the bottom of the file.

```
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/1.11/howto/static-files/
PROJECT_ROOT   =   os.path.join(os.path.abspath(__file__))
STATIC_ROOT  =   os.path.join(PROJECT_ROOT, 'staticfiles')
STATIC_URL = '/static/'

# Extra lookup directories for collectstatic to find static files
STATICFILES_DIRS = (
    os.path.join(PROJECT_ROOT, 'static'),
)

#  Add configuration for static files storage using whitenoise
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

```

8. Add whitenoise middleware at the top of the middleware list in settings.py

```
MIDDLEWARE = [
    'whitenoise.middleware.WhiteNoiseMiddleware',
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

9.  Update Database Configuration in settings.py (at the bottom of the file)

```
import dj_database_url 
prod_db  =  dj_database_url.config(conn_max_age=500)
DATABASES['default'].update(prod_db)
```

10. Create App in Heroku from terminal
```
(django_env):~/Desktop/djangoherokuapp$ heroku create herokudjangoapp
```
App created:
https://herokudjangoapp-deployheroku.herokuapp.com/ | https://git.heroku.com/herokudjangoapp-deployheroku.git


11. Add your app domain name to ALLOWED_HOSTS in settings.py.
```
ALLOWED_HOSTS = ['herokudjangoapp-deployheroku.herokuapp.com']
```

12. Initialize Git and connect your new app (or existing one) to Heroku Git remote repository.
```
(django_env):~/Desktop/djangoherokuapp$ git init
(django_env):~/Desktop/djangoherokuapp$ heroku git:remote -a herokudjangoapp-deployheroku

set git remote heroku to https://git.heroku.com/herokudjangoapp-deployheroku.git

(django_env):~/Desktop/djangoherokuapp$ git add .
(django_env):~/Desktop/djangoherokuapp$ git commit -m "Initial commit"

(django_env):~/Desktop/djangoherokuapp$ heroku config:set     DISABLE_COLLECTSTATIC=1

(django_env):~/Desktop/djangoherokuapp$ git push heroku master
```

13. Migrate the database
```
(django_env):~/Desktop/djangoherokuapp$ heroku run python manage.py migrate
```

14. Visit https://herokudjangoapp-deployheroku.herokuapp.com/
