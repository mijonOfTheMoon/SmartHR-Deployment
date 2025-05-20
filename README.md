# SmartHR Deployment

This repository contains the deployment configuration for the SmartHR application.

## Prerequisites

- [Git](https://git-scm.com/downloads)
- [Docker](https://www.docker.com/products/docker-desktop/)
- [Docker Compose](https://docs.docker.com/compose/install/) (included with Docker Desktop)

## Getting Started

### Clone the Repository

```bash
git clone https://github.com/mijonOfTheMoon/SmartHR-Deployment
cd SmartHR-Deployment
```

### Start the Application

Run the following command to start the application in detached mode:

```bash
docker compose up -d
```

This will:
- Start the web application on port 8080
- Start the MySQL database on port 3306
- Create a persistent volume for the database

Seed the database if you haven't do it before

```bash
docker exec "smarthr-deployment-web-1" sh -c "php artisan migrate:fresh --seed --force && php artisan module:migrate --all --seed --force"
```

### Access the Application

Once the containers are running, you can access the application at:

```
http://localhost:8080
```

### Stop the Application

To stop the application, run:

```bash
docker compose down
```

To stop the application and remove the volumes:

```bash
docker compose down -v
```

# Cara membuat image adsis/smart-hr:stable

Clone dan pindah ke dalam folder source code

```bash
git clone https://github.com/MusheAbdulHakim/Laravel-Smarthr
cd Laravel-Smarthr
```

Buat file .env baru.

```bash
nano .env
```

Isi file .env sebagai berikut.

```bash
APP_NAME=SmartHR
APP_ENV=production
APP_KEY=
APP_DEBUG=false
APP_TIMEZONE=Asia/Jakarta
APP_URL=http://localhost

APP_LOCALE=en
APP_FALLBACK_LOCALE=en
APP_FAKER_LOCALE=en_US

APP_MAINTENANCE_DRIVER=file
APP_MAINTENANCE_STORE=database

BCRYPT_ROUNDS=12

LOG_CHANNEL=stack
LOG_STACK=single
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

SESSION_DRIVER=database
SESSION_LIFETIME=120
SESSION_ENCRYPT=false
SESSION_PATH=/
SESSION_DOMAIN=null

BROADCAST_CONNECTION=log
FILESYSTEM_DISK=local
QUEUE_CONNECTION=database

CACHE_STORE=database
CACHE_PREFIX=

MEMCACHED_HOST=127.0.0.1

VITE_APP_NAME="${APP_NAME}"

PUSHER_HOST="${REVERB_HOST}"
PUSHER_APP_KEY="${REVERB_APP_KEY}"
PUSHER_APP_SECRET="${REVERB_APP_SECRET}"
PUSHER_APP_ID="${REVERB_APP_ID}"
PUSHER_PORT="${REVERB_PORT}"
```
Buat file .dockerignore baru.

```bash
nano .dockerignore
```

Isi file .dockerignore sebagai berikut.

```bash
.editorconfig
.env.example
.git
.gitattributes
.github
.gitignore
.dockerignore
README.md
Dockerfile
```

Buat file Dockerfile baru.

```bash
nano Dockerfile
```

Isi file Dockerfile sebagai berikut.

```bash
FROM serversideup/php:8.2-fpm-nginx-alpine
WORKDIR /var/www/html

USER root
RUN docker-php-serversideup-dep-install-alpine npm
RUN install-php-extensions exif gd bcmath

USER www-data
COPY --chown=www-data:www-data . .
RUN composer install --no-cache && composer clear-cache
RUN php artisan key:generate
RUN php artisan reverb:install
RUN php artisan storage:link
RUN npm install --no-cache && npm run build && npm cache clean --force && rm -rf node_modules
```

Build imagenya.

```bash
docker build -t adsis/smart-hr:stable
```