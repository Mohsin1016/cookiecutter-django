# Django Cookiecutter Project Guide

![Django](https://img.shields.io/badge/Django-Latest-green) ![Docker](https://img.shields.io/badge/Docker-Latest-blue) ![Cookiecutter](https://img.shields.io/badge/Cookiecutter-Latest-orange)

A comprehensive guide for working with Django projects using Cookiecutter and Docker. This guide covers project setup, development workflows, production deployment, and common troubleshooting scenarios.

## Table of Contents

- [Installation](#installation)
  - [Installing Cookiecutter](#installing-cookiecutter)
  - [Creating a New Project](#creating-a-new-project)
- [Project Structure](#project-structure)
  - [Core Directories](#core-directories)
  - [Key Components Explained](#key-components-explained)
- [Development](#development)
  - [Docker Commands](#docker-commands)
  - [Creating a New App](#creating-a-new-app)
  - [Working with Serializers](#working-with-serializers)
  - [Installing Python Packages](#installing-python-packages)
  - [Environment Variables](#environment-variables)
- [Production](#production)
  - [Model Changes in Production](#model-changes-in-production)
  - [Domain Change Procedure](#domain-change-procedure)
  - [Static & Media Files](#static--media-files)
- [Database Management](#database-management)
  - [Creating Backups](#creating-backups)
  - [Restoring Backups](#restoring-backups)
- [Troubleshooting](#troubleshooting)
  - [Gunicorn Worker Timeout](#gunicorn-worker-timeout)

## Installation

### Installing Cookiecutter

Install Cookiecutter using pip:

```bash
pip install cookiecutter
```

### Creating a New Project

Scaffold a new Django project with Cookiecutter:

```bash
cookiecutter https://github.com/cookiecutter/cookiecutter-django
```

You will be prompted with a series of configuration questions. Below is a sample setup:

#### Project Information

| Prompt       | Sample Input                 |
| ------------ | ---------------------------- |
| project_name | project_name                 |
| project_slug | (press Enter)                |
| description  | (press Enter)                |
| author_name  | Mohsin                       |
| domain_name  | (press Enter)                |
| email        | muhammadmohsin1016@gmail.com |
| version      | (press Enter)                |

#### License Configuration

| Sample Input        | Prompt                     |
| ------------------- | -------------------------- |
| 5 - Not open source | Select open_source_license |

#### User Configuration

| Sample Input | Prompt               |
| ------------ | -------------------- |
| 2 - email    | Select username_type |

#### Environment Configuration

| Prompt   | Sample Input  |
| -------- | ------------- |
| timezone | (press Enter) |
| windows  | y             |

#### Editor & Docker Configuration

| Prompt        | Sample Input |
| ------------- | ------------ |
| Select editor | 3 - VS Code  |
| use_docker    | y            |

#### Database & Cloud Configuration

| Prompt                    | Sample Input              |
| ------------------------- | ------------------------- |
| Select postgresql_version | (press Enter for default) |
| Select cloud_provider     | 2 - GCP                   |

#### Mail Configuration

| Sample Input   | Prompt              |
| -------------- | ------------------- |
| 9 - Other SMTP | Select mail_service |

#### Features Configuration

| Prompt                   | Sample Input  |
| ------------------------ | ------------- |
| use_async                | y             |
| use_drf                  | y             |
| Select frontend_pipeline | 1 - None      |
| use_celery               | y             |
| use_mailpit              | y             |
| use_sentry               | y             |
| use_whitenoise           | y             |
| use_heroku               | (press Enter) |

#### Continuous Integration Configuration

| Prompt                 | Sample Input          |
| ---------------------- | --------------------- |
| Select ci_tool         | 4 - Github            |
| keep_local_envs_in_vcs | (press Enter for Yes) |
| debug                  | y                     |

Once completed, you should see:

> ✅ [SUCCESS]: Project initialized, keep up the good work!


## Project Structure

The Django Cookiecutter project follows a well-organized directory structure that separates configuration, application code, and deployment settings.

### Core Directories

```
practice_project/
├── config/               # Project configuration
│   ├── settings/         # Environment-specific settings
│   │   ├── base.py       # Base settings shared across environments
│   │   ├── local.py      # Development environment settings
│   │   └── production.py # Production environment settings
│   ├── urls.py           # Root URL configuration
│   └── wsgi.py           # WSGI application entry point
│
├── practice_project/     # Main application code
│   ├── users/            # User management app
│   ├── static/           # Static assets (CSS, JS, images)
│   ├── templates/        # HTML templates
│   └── contrib/          # Additional Django components
│
├── compose/              # Docker compose configurations
│   ├── local/            # Development environment containers
│   └── production/       # Production environment containers
│
├── docs/                 # Project documentation
│
├── requirements/         # Python dependencies
│   ├── base.txt          # Base requirements for all environments
│   ├── local.txt         # Development-specific requirements
│   └── production.txt    # Production-specific requirements
│
└── manage.py             # Django command-line utility
```

### Key Components Explained

#### Configuration (`config/`)

Contains all project-wide configuration including settings, URL routing, and application entry points:

- `settings/` - Separated settings for different environments
- `urls.py` - Root URL dispatcher
- `api_router.py` - DRF API routing configuration
- `wsgi.py` & `asgi.py` - Web server entry points

#### Application (`practice_project/`)

Contains the actual application code:

- `users/` - Custom user model and authentication handling
- `static/` - CSS, JavaScript, and image assets
- `templates/` - HTML templates with Django template language
- `contrib/` - Additional Django apps and integrations

#### Docker Configuration (`compose/`)

Docker Compose configurations for different environments:

- `local/` - Development environment with hot-reloading
- `production/` - Production-ready containers with optimized settings

#### Requirements (`requirements/`)

Python package dependencies for different environments:

- `base.txt` - Core dependencies used in all environments
- `local.txt` - Development tools and debugging utilities
- `production.txt` - Production-specific packages and optimizations

## Development

### Docker Commands

Use these Docker Compose commands to manage your Django project effectively:

#### Build and Start Containers

```bash
docker compose -f ./docker-compose.local.yml up --build
```

This builds and starts all services defined in the docker-compose.local.yml file.

#### Stop and Remove Containers

```bash
docker compose -f ./docker-compose.local.yml down
```

This stops and removes all running containers, networks, and volumes created by up.

#### Run Migrations

```bash
docker compose -f ./docker-compose.local.yml run --rm django python manage.py migrate
```

Runs Django database migrations. The `--rm` flag ensures the container is removed after the command completes.

#### Create New Migrations

```bash
docker compose -f ./docker-compose.local.yml run --rm django python manage.py makemigrations
```

Generates new migration files based on changes to your models.

#### Create Superuser

```bash
docker compose -f docker-compose.local.yml run --rm django python manage.py createsuperuser
```

Creates an admin user for accessing the Django admin interface.

### Creating a New App

Follow these steps to create a new Django app in a Cookiecutter project:

#### 1. Create the App Using Docker Compose

```bash
docker compose -f ./docker-compose.local.yml run --rm django python manage.py startapp test
```

This creates a new Django app named `test` in your project directory.

#### 2. Move the App to the Project Package

Move the newly created `test` folder into the main Django project folder. The structure should look like:

```
inspiration_animation/
├── __init__.py
├── settings/
├── urls.py
├── wsgi.py
├── asgi.py
├── users/
└── test/   <- Move here
```

#### 3. Update App Configuration

Navigate to `inspiration_animation/test/apps.py` and update the name attribute:

```python
# BEFORE:
name = "test"

# AFTER:
name = "inspiration_animation.test"
```

#### 4. Register the App

Open `config/settings/base.py` and add the new app to the `LOCAL_APPS` list:

```python
LOCAL_APPS = [
    "inspiration_animation.users",
    "inspiration_animation.test",
    # Your stuff: custom apps go here
]
```

#### 5. Configure App URLs

Open `config/urls.py` and include the app's URLs:

```python
from django.urls import path, include

urlpatterns = [
    # Other URLs...
    path("test/", include("inspiration_animation.test.urls", namespace="test")),
]
```

#### 6. Create App URL Configuration

Create a new file `inspiration_animation/test/urls.py` with the following content:

```python
from django.urls import path

app_name = "test"

urlpatterns = [
    # Add your app routes here
]
```

### Working with Serializers

Serializers convert Django model instances (or querysets) into JSON and vice versa.

#### What Are Serializers?

Serializers act as translators between your database models and your API (JSON ↔ Python objects).

#### Why Serializers Are Important

| Use Case     | Purpose                                                  |
| ------------ | -------------------------------------------------------- |
| API Output   | Convert model data into JSON for API responses           |
| API Input    | Validate and deserialize incoming JSON to Python objects |
| Validation   | Ensure data integrity before saving to the database      |
| Security     | Control which fields are exposed (e.g., hide passwords)  |
| Custom Logic | Add computed fields, nested data, or custom save logic   |

#### Example Implementation

```python
# myapp/serializers.py
from rest_framework import serializers
from .models import User

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'email', 'name']
```

Usage in a view:

```python
# myapp/views.py
from rest_framework.response import Response
from .serializers import UserSerializer

def my_api_view(request):
    user = User.objects.first()
    serializer = UserSerializer(user)
    return Response(serializer.data)
```

### Installing Python Packages

To add new libraries to your Docker-based Django project:

#### 1. Find the Package on PyPI

1. Visit https://pypi.org
2. Search for the desired package (e.g., openai)
3. Copy the latest version number

#### 2. Add the Package to Requirements

Navigate to `requirements/base.txt` and add the package:

```
# Recommended approach - pin the version
openai==1.31.1

# Alternative - always get latest version
# openai
```

#### 3. Rebuild and Restart Docker Services

```bash
docker compose -f docker-compose.production.yml down
docker compose -f docker-compose.production.yml up -d --build
```

Docker will automatically detect changes in requirements/base.txt and install the new packages.

#### Best Practices

- Pin versions (`==`) for production stability
- Keep requirements organized
- Rebuild Docker anytime you change dependencies

### Environment Variables

#### Environment Files for Docker

Store credentials securely in these files:

- `.envs/.local/.django` — for local development
- `.envs/.production/.django` — for production

Example format:

```
# API Keys & Secrets
COMPOSIO_API_KEY=your_api_key_here
OUTLOOK_CLIENT_ID=your_client_id
DJANGO_SECRET_KEY=your_secret_key

# Debug and config
DJANGO_DEBUG=False
```

These are automatically loaded by Docker via `env_file` in docker-compose files.

#### Using Credentials in Django Settings

In `config/settings/base.py`, access values like this:

```python
import environ
env = environ.Env()

SECRET_KEY = env("DJANGO_SECRET_KEY")
DEBUG = env.bool("DJANGO_DEBUG", default=False)

COMPOSIO_API_KEY = env("COMPOSIO_API_KEY")
OUTLOOK_CLIENT_ID = env("OUTLOOK_CLIENT_ID")
```

#### Best Practices

- Never hardcode secrets in settings
- Do not commit `.envs/` files to Git
- Use different values for development and production
- Document required environment variables
- Restart Docker when `.env` files change:

```bash
docker compose -f docker-compose.production.yml down
docker compose -f docker-compose.production.yml up -d --build
```

## Production

### Model Changes in Production

> ⚠️ **Important**: Never make changes to Django models directly on the production server.

#### Recommended Workflow

1. **Make changes locally**

   - Modify your Django models on your local development machine

2. **Use local Docker Compose**

   ```bash
   docker compose -f ./docker-compose.local.yml run --rm django python manage.py makemigrations
   docker compose -f ./docker-compose.local.yml run --rm django python manage.py migrate
   ```

3. **Push changes to GitHub**

   - After verifying your changes locally, push the updated code (including migration files) to your GitHub repository

4. **Deploy to Production**

   - On the production server:

   ```bash
   # Stop the project
   docker compose -f ./docker-compose.prod.yml down
   
   # Pull latest code from GitHub
   git pull
   
   # Rebuild and start the containers
   docker compose -f ./docker-compose.prod.yml up --build -d
   
   # Apply migrations
   docker compose -f ./docker-compose.prod.yml run --rm django python manage.py migrate
   ```

#### Why This Matters

Directly changing models in production can cause:

- Data inconsistency
- Migration conflicts
- Downtime or broken features

Always follow this safe deployment process to maintain stability and reliability.

### Domain Change Procedure

Follow these steps to change the domain name in a production environment:

1. **Apply specific migration for sites**

   ```bash
   docker compose -f docker-compose.production.yml run --rm django python manage.py migrate sites 0002
   ```

2. **Check applied migrations for sites**

   ```bash
   docker compose -f docker-compose.production.yml run --rm django python manage.py showmigrations sites
   ```

3. **Replace domain in Django**

   - Update domain references in settings and configuration

4. **Rebuild Docker containers**

   ```bash
   docker compose -f docker-compose.production.yml build
   ```

5. **Run all pending migrations**

   ```bash
   docker compose -f docker-compose.production.yml run --rm django python manage.py migrate
   ```

6. **Restart services in background**

   ```bash
   docker compose -f docker-compose.production.yml up -d --build
   ```

### Static & Media Files

#### Static Files in Production

Cookiecutter Django uses WhiteNoise for static files by default:

```bash
docker compose -f docker-compose.production.yml run --rm django python manage.py collectstatic
```

#### Media Files in Production

Store media files in cloud storage (e.g., AWS S3 or GCP bucket) for production.

## Database Management

### Restoring a Production Database Backup

#### 1. Create a Compressed Dump on the Production VM

```bash
docker exec -i <postgres_container> \
  bash -c "PGPASSWORD='<db_password>' \
  pg_dump -U <db_user> -d <db_name> -F c" \
  > <host_path>/<dump_file>.dump
```

Example:

```bash
docker exec -i production-postgres-1 \
  bash -c "PGPASSWORD='xD4Jk01o7Xbct129wefNVwfzyrmiK3W0686w2QARsl46NiRpCwtAGb0g2mOVRz' \
  pg_dump -U user -d dev_db -F c" \
  > /home/db-backups/dev-db.dump
```

The `-F c` flag creates a custom-format dump (better for pg_restore).

#### 2. Copy the Dump to Your Local Machine

```bash
scp techanzy@34.69.171.24:/home/techanzy/db-backups/b2r-devdb-001.dump ~/Downloads/
```

#### 3. Build & Start Local Containers

```bash
docker compose -f ./docker-compose.local.yml up --build
```

#### 4. Copy the Dump into the Local Postgres Container

```bash
docker cp ~/Downloads/b2r-devdb-001.dump b2r_email_automation_local_postgres:/tmp/
```

#### 5. Restore the Dump

```bash
docker exec -it b2r_email_automation_local_postgres \
  pg_restore -U PnlkLbTuHApoDVihZtpmiqNqcTUTTYtB \
  -d br2_email_automation -v /tmp/b2r-devdb-001.dump
```

#### 6. Troubleshooting: Re-Create the Local DB

If the restore fails, drop & recreate the database, then restore again:

```bash
# Stop Django container
docker stop b2r_email_automation_local_django

# 1. Drop old DB (connect to default 'postgres' first)
docker exec -it b2r_email_automation_local_postgres \
  psql -U PnlkLbTuHApoDVihZtpmiqNqcTUTTYtB -d postgres \
  -c "DROP DATABASE IF EXISTS br2_email_automation;"

# 2. Create fresh DB
docker exec -it b2r_email_automation_local_postgres \
  psql -U PnlkLbTuHApoDVihZtpmiqNqcTUTTYtB -d postgres \
  -c "CREATE DATABASE br2_email_automation;"

# 3. Restore without owner / ACL
docker exec -it b2r_email_automation_local_postgres \
  pg_restore -U PnlkLbTuHApoDVihZtpmiqNqcTUTTYtB \
  -d br2_email_automation --no-owner --no-acl \
  /tmp/b2r-devdb.dump

# 4. Restart Django container (if needed)
docker start b2r_email_automation_local_django
```

#### 7. Verify the Restore

```bash
# List all DBs
docker exec -it b2r_email_automation_local_postgres \
  psql -U PnlkLbTuHApoDVihZtpmiqNqcTUTTYtB -d br2_email_automation \
  -c "\l"

# Count users table rows
docker exec -it b2r_email_automation_local_postgres \
  psql -U PnlkLbTuHApoDVihZtpmiqNqcTUTTYtB -d br2_email_automation \
  -c "SELECT COUNT(*) FROM users_user;"
```

## Troubleshooting

### Gunicorn Worker Timeout

#### Issue

Error Message:

```
[CRITICAL] WORKER TIMEOUT (pid:XXX)
```

This occurs when a request takes longer than the allowed time limit (default: 30 seconds). Gunicorn kills the worker process to prevent it from hanging indefinitely.

#### Common Causes

- Long-running API calls (e.g., fetching recurring Outlook events)
- Large database queries
- Inefficient or unoptimized code

#### Diagnosis

The project uses Cookiecutter Django with Docker. In production, Django is launched via the script `compose/production/django/start`. This script runs the following command:

```bash
exec /usr/local/bin/gunicorn config.asgi --bind 0.0.0.0:5000 --chdir=/app -k uvicorn_worker.UvicornWorker
```

Gunicorn is used with the UvicornWorker for running the ASGI app.

#### Solution

1. **Increase Gunicorn Timeout**

   Edit the file: `compose/production/django/start`

   Update this line:

   ```bash
   exec /usr/local/bin/gunicorn config.asgi --bind 0.0.0.0:5000 --chdir=/app -k uvicorn_worker.UvicornWorker
   ```

   To:

   ```bash
   exec /usr/local/bin/gunicorn config.asgi --bind 0.0.0.0:5000 --chdir=/app -k uvicorn_worker.UvicornWorker --timeout 120
   ```

   `--timeout 120` sets the timeout to 120 seconds. Adjust if needed.

2. **Rebuild and Restart Docker**

   ```bash
   docker compose -f docker-compose.production.yml up --build
   ```

   This applies the new timeout setting.



