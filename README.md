# This repository is base repos. it used to create the app at the first step.

1. backend folder: it is backend code of application, it also support api to frontend
- It containers laravel and Dockerfile. At the current. It is only Dockerfile

2. deployment: this folder holding the config files
    

# build images:
1. Dockerizing a Laravel Backend
- create the laravel resource
    composer create-project laravel/laravel .
- docker build -t api:0.1 -f ./api/Dockerfile --build-arg user=butachi --build-arg uid=1000 .
- docker run -it --rm api:0.1

2. Dockerizing a scheduler and a worker


# docker compose with services
- backend: it's related to laravel & php-fpm
- database:
redis & mysql
- migrate:
from backend/Dockerfile to migrate data
- nginx:
it's related to web server (api:9000)
- reverse proxy:
it redirect to frontend or nginx container
- scheduler and worker:
They are basically the same as the api service but they target specific build stages.
Supervisor is running in worker service.
- update:
Run file sh to execute the php artisan scripts:

# Run command for development
- Start the application (-d runs in detached mode so it runs in the background)
```
docker compose up or docker compose up -d 
```

- Execute commands in a give container. 
```
docker compose exec api bash (php artisan tinker)
```

- Restart the given service. We often use this command to restart the worker
```
docker compose restart <service>
```

- Rebuilds the images and then starts the whole project. It's useful when you're modifying the Dockerfile and test if it works
```
docker compose up --build
```

- Show log of given service. If you don't specify the service it shows every container.
```
docker compose log -f --tail=400 <service>
```

- Stops and removes containers.
```
docker compose down
```
# Manually building from the source
- Clone this repo `git clone https://github.com/butachi/email-marketing.git`
- `copy .env-example .env`
- `cd backend` then `cp .env-example .env` and `composer install`
- At root folder. run command `docker compose up`
- Check url: http://localhost:8000/

