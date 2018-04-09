# Building Forums with Django Machina

There is [related presentation](django-machina.html) which is used during the presentation. 

## Steps for installation

1. Create virtualenv and activate it
    1. `virtualenv ~/Installs/env/forums`
    1. `source ~/Installs/env/forums/bin/activate`
1. Install dependencies using Pip
    1. `pip install Django==1.11`
    1. `pip install django-machina Whoosh==2.7.4`
1. Create a new project
    1. `django-admin.py startproject hello_machina`
1. Update following sections in `settings.py` {or `base.py`}
    1. `INSTALLED_APPS`
        - `from machina import get_apps as get_machina_apps`
        -  Add following below `staticfiles`
            ```python
            'mptt',
            'haystack',
            'widget_tweaks',
            ```
        - Add following after the list of `INSTALLED_APPS`
            ```python
             + get_machina_apps()
            ```
    1. `TEMPLATES`
        - `from machina import MACHINA_MAIN_TEMPLATE_DIR`
        - Add following to `DIRS` section of `TEMPLATES`  
            - `MACHINA_MAIN_TEMPLATE_DIR,`
        - Add following to `context_processors` section in `OPTIONS`
            - `'machina.core.context_processors.metadata',`
    1. `MIDDLEWARE`
        - Add following to Middleware
            - `'machina.apps.forum_permission.middleware.ForumPermissionMiddleware',`
    1. `STATICFILES_DIRS`
        - This might not have been created by default add following section
        ```python
        STATICFILES_DIRS = (
            MACHINA_MAIN_STATIC_DIR,
        )
        ```
    1. `CACHES`
        - This section might have not been added as-well
        ```python
        CACHES = {
            'default': {
                'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
            },
            'machina_attachments': {
                'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
                'LOCATION': '/tmp',
            },
        }
        ```
1. Setting up Django Haystack, update following sections in `settings.py` {or `base.py`}
    1. `HAYSTACK_CONNECTIONS`
        - We've already installed Woosh so we need to add following section towards end of our `setting.py` file
        ```python
        HAYSTACK_CONNECTIONS = {
            'default': {
                'ENGINE': 'haystack.backends.whoosh_backend.WhooshEngine',
                'PATH': os.path.join(BASE_DIR, 'whoosh_index'),
            },
        }
        ```
1. Run migrations `python manage.py migrate`
1. Update `urls.py`
    - `from machina.app import board`
    -  Add `url(r'^forum/', include(board.urls)),` to our patterns
1. Check if everything is working fine after running the server, visit `http://localhost:8000/forum/` and see if your forum renders

## Creating Topics

1. Make sure a superuser has been created
    `python manage.py createsuperuser`
1. Logon to Admin `http://localhost:8000/admin`
1. Make sure to update Global Settings
1. Create a Forum
1. Create few Topics that you would like