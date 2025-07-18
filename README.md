# Apache Airavata Django Portal

![Build Status](https://github.com/apache/airavata-django-portal/actions/workflows/build-and-test.yaml/badge.svg)
[![Docs](https://readthedocs.org/projects/apache-airavata-django-portal/badge/?version=latest)](https://apache-airavata-django-portal.readthedocs.io/en/latest/?badge=latest)

A modern, extensible web portal for [Apache Airavata](http://airavata.apache.org/) built with Django, Vue.js, and Wagtail CMS. Designed for science gateways and research platforms, with plugin support and a robust API.

---

## 🚀 Features
- Full-featured science gateway UI for Airavata
- Customizable via plugins and Wagtail CMS
- Modern Vue.js frontend (built with Yarn workspaces)
- REST API and Django admin
- Dockerized for easy deployment
- MySQL, Postgres, or SQLite support

---

## 🐳 Quick Start (Production with Docker)

### 1. Build the Docker Image
```bash
docker build -t airavata-django-portal .
```

### 2. Start a MySQL Test Container
```bash
docker run -d \
  --name mysql-airavata \
  -e MYSQL_ROOT_PASSWORD=rootpass \
  -e MYSQL_DATABASE=airavata_db \
  -e MYSQL_USER=airavata_user \
  -e MYSQL_PASSWORD=airavata_pass \
  -p 3306:3306 \
  mysql:8.0
```

### 3. Prepare `settings_local.py`
Create `django_airavata/settings_local.py` with:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'airavata_db',
        'USER': 'airavata_user',
        'PASSWORD': 'airavata_pass',
        'HOST': 'mysql-airavata',
        'PORT': '3306',
    }
}
import os
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

### 4. Run the Portal (Mounting settings_local.py)
```bash
docker run -d \
  --name airavata-app \
  --link mysql-airavata:mysql \
  -p 8000:8000 \
  -v $(pwd)/django_airavata/settings_local.py:/app/django_airavata/settings_local.py \
  airavata-django-portal
```

### 5. Run Migrations & Collect Static Files
```bash
docker exec -it airavata-app python manage.py migrate
docker exec -it airavata-app python manage.py collectstatic --noinput
```

- App available at: http://localhost:8000/home/
- Logs: `docker logs airavata-app`

---

## 💻 Local Development Setup

### 1. Clone & Python Virtualenv
```bash
git clone https://github.com/apache/airavata-django-portal.git
cd airavata-django-portal
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
```

### 2. Node.js & Yarn
- Use **Node.js 18+** (or 20+ if all dependencies support it)
- Install Yarn classic:
  ```bash
  npm install -g yarn
  yarn --version
  ```

### 3. Create/Edit `settings_local.py`
- Copy from sample:
  ```bash
  cp django_airavata/settings_local.py.sample django_airavata/settings_local.py
  # Edit as needed for DB, Airavata, Keycloak, etc.
  ```

### 4. Build Frontend Assets
```bash
./build_js.sh
```

### 5. Run Migrations & Load CMS Data
```bash
python manage.py migrate
python manage.py load_cms_data new_default_theme
```

### 6. Start the Dev Server
```bash
python manage.py runserver
```

- Visit: http://localhost:8000/home/

---

## 🧩 Customization & Plugins
- Extend via Django apps or Wagtail CMS
- See [Customization Guide](https://apache-airavata-django-portal.readthedocs.io/en/latest/)
- Add Vue.js components in `static/common/components/`

---

## 🧪 Testing & Development
- **Run tests:** `./runtests.py`
- **Lint Python:** `flake8 .`
- **Format Python:** `autopep8 --in-place --recursive .`
- **Lint/Format JS:** `yarn lint` / `yarn prettier`
- **Dev docs:** See `docs/dev/`

---

## 📚 Documentation
- [Read the Docs](https://apache-airavata-django-portal.readthedocs.io/en/latest/)
- Build locally: `mkdocs serve`

---

## 🤝 Contributing
- Fork, branch, and submit PRs!
- See [Get Involved](http://airavata.apache.org/get-involved.html)
- Use [Jira](https://issues.apache.org/jira/projects/AIRAVATA) for bugs/features (select _Django Portal_ component)

---

## 📝 License
Apache License 2.0 — see [LICENSE](LICENSE)

---

## ❓ Troubleshooting & FAQ

- **Static files not loading?**
  - Make sure you ran `collectstatic` after building frontend assets.
  - Check `STATIC_ROOT` in your settings.
- **DB connection errors in Docker?**
  - Use `DB_HOST='mysql-airavata'` (not `localhost`)
  - Ensure MySQL container is running and healthy.
- **Frontend build fails (minimatch/node version)?**
  - Use Node 20+ if needed, or restore original `yarn.lock`.
- **Live reload for development?**
  - Mount the whole codebase as a volume and use Django’s dev server.
- **Need to reset DB?**
  - Drop/recreate the MySQL DB, then rerun migrations and `load_cms_data`.

---

For more help, email <dev@airavata.apache.org> or open an issue!
