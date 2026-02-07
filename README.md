# Kittygram

Веб‑приложение для публикации карточек котов, их достижений и фотографий. Проект состоит из backend‑части на Django, frontend‑части на React и Nginx‑шлюза. Приложение разворачивается в Docker‑контейнерах и использует CI/CD на GitHub Actions для автоматического тестирования, сборки и деплоя.

![Main Kittygram Workflow](https://github.com/0legRogovenko/kittygram_final/actions/workflows/main.yml/badge.svg)


## Описание

Kittygram позволяет:

- создавать профили котов с фотографиями  
- добавлять достижения  
- просматривать карточки других пользователей  
- работать с API через Django REST Framework  
- загружать изображения  
- использовать админ‑панель Django  
- автоматически деплоить проект на сервер через GitHub Actions  

## Технологический стек

### Backend
- Python 3.9  
- Django 3.2  
- Django REST Framework  
- PostgreSQL  
- Gunicorn  

### Frontend
- React  
- Node.js 18  

### DevOps
- Docker, Docker Compose  
- Nginx  
- GitHub Actions  
- Docker Hub  

## Установка и запуск

### 1. Клонирование репозитория

```
git clone https://github.com/<your_username>/kittygram.git
cd kittygram
```

### 2. Создание файла `.env`

Пример:

```
POSTGRES_DB=kittygram_db
POSTGRES_USER=kittygram_user
POSTGRES_PASSWORD=kittygram_password_1
DB_HOST=db
DB_PORT=5432

SECRET_KEY=your_secret_key
DEBUG=False
ALLOWED_HOSTS=127.0.0.1, localhost
USE_SQLITE=false
```

### 3. Запуск проекта в Docker

```
sudo docker compose -f docker-compose.production.yml up -d --build
```

### 4. Применение миграций и сборка статики

```
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic --noinput
```

## Структура контейнеров

- db — PostgreSQL  
- backend — Django + Gunicorn  
- frontend — React‑сборка  
- gateway — Nginx  
- volumes:
  - static_volume  
  - media_volume  
  - pg_data_production  

## Конфигурация Nginx

```
server {
  listen 80;
  index index.html;
  server_tokens off;
  client_max_body_size 20M;

  location /api/ {
    proxy_set_header Host $http_host;
    proxy_pass http://backend:8000/api/;
  }

  location /admin/ {
    proxy_set_header Host $http_host;
    proxy_pass http://backend:8000/admin/;
  }

  location /static/ {
    alias /static/;
  }

  location /media/ {
    alias /media/;
  }

  location / {
    alias /static/;
    try_files $uri $uri/ /index.html;
  }
}
```

## CI/CD

GitHub Actions выполняет:

### При пуше в любую ветку:
- тестирование backend на нескольких версиях Python  
- flake8  
- тестирование frontend  

### При пуше в master:
- сборка Docker‑образов backend, frontend, gateway  
- публикация образов в Docker Hub  
- деплой на сервер  
- отправка уведомления в Telegram с:
  - автором пуша  
  - сообщением коммита  
  - ссылкой на коммит  

## Как запустить автотесты Практикума

Создайте файл `tests.yml` в корне проекта:

```
repo_owner: <ваш_логин_на_гитхабе>
kittygram_domain: https://<ваш_домен_kittygram>
taski_domain: https://<ваш_домен_taski>
dockerhub_username: <ваш_логин_на_dockerhub>
```

Скопируйте `.github/workflows/main.yml` в `kittygram_workflow.yml`.

Для локального запуска тестов:

```
python -m venv venv
source venv/bin/activate
pip install -r backend/requirements.txt
pytest
```

## Чек‑лист перед сдачей

- Kittygram доступен по домену `https://kittygramo.duckdns.org`  
- Taski доступен по домену `https://taskir.myddns.me/`  
- Пуш в master запускает тесты и деплой  
- После деплоя приходит сообщение в Telegram  
- В корне есть файл `kittygram_workflow.yml`  

## Автор

Олег Роговенко  
GitHub: `https://github.com/0legRogovenko/`
