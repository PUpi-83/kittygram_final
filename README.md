# Kittygram - интернет-площадка, посвященная обожаемым мурлыкам и их прекрасным фотографиям :octocat:

## О проекте
**Kittygram** - это проект, разработанный в рамках учебного курса по Python от Яндекс.Практикум. Это уникальная социальная платформа, предназначенная исключительно для обмена фотографиями котиков! Здесь вы можете зарегистрироваться, загружать снимки своих милых котиков, добавлять к ним краткие описания и наслаждаться просмотром котиков других пользователей.

**Развёрнутое приложение можно посмотреть по [ссылке](https://purrterritory.sytes.net/)**

## Технологии
-   Python 3.9
-   Django==3.2.3
-   djangorestframework==3.12.4
-   Nginx
-   Gunicorn
-   Docker
-   Postgres

## Устанавливаем проект
-   Склонируйте и перейдите в корневую директорию проекта:
```
git clone https://github.com/PUpi-83/kittygram_final.git
```
-   Создайте файл с названием .env и заполните его данными следующим образом:
```
# Секреты DB
POSTGRES_DB=*имя_базы*
POSTGRES_USER=*имя_пользователя_базы*
POSTGRES_PASSWORD=*пароль_к_базе*
DB_HOST=*db*
DB_PORT=*порт_соединения_к_базе*

# Секреты django
SECRET_KEY = *'SECRET_KEY'*
DEBUG=True
```
## Собираем Docker-образы
-   Замените username на ваш логин, с которым Вы зарегистрировались DockerHub:
```
cd frontend
docker build -t username/kittygram_frontend .
cd ../backend
docker build -t username/kittygram_backend .
cd ../nginx
docker build -t username/kittygram_gateway . 
```
-   Загрузите образы на DockerHub:
```
docker push username/kittygram_frontend
docker push username/kittygram_backend
docker push username/kittygram_gateway
```

## Деплой на удалённый сервер
-   Подключитесь к удаленному серверу:
```
ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом имя_пользователя@ip_адрес_сервера 
```
-   Через терминал, на сервере, создайте директорию kittygram:
```
mkdir kittygram
```
-   Установите Docker Compose на сервер:
```
sudo apt update
sudo apt install curl
curl -fSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
sudo apt-get install docker-compose-plugin
```
-   В директорию kittygram/ скопируйте файлы docker-compose.production.yml и .env:
```
scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/kittygram/docker-compose.production.yml
```
-  **Если через команды не получилось скопировать файлы, то создайте их и внесите данные вручную :**
```
sudo nano docker-compose.production.yml
sudo nano .env
```
-   Запустите docker compose в режиме демона:
```
sudo docker compose -f docker-compose.production.yml up -d
```
-   Выполните миграции, соберите статику бэкенда и скопируйте их в /backend_static/static/:
```
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
```
-   На сервере, в редакторе Nano, откройте конфиг Nginx:
```
sudo nano /etc/nginx/sites-enabled/default
```
-   Добавте настройки location в секции server:
```
location / {
    proxy_set_header Host $http_host;
    proxy_pass http://127.0.0.1:9000;
}
```
-  Проверьте работоспособность файла конфигураций и перезапустите Nginx:
``` 
sudo nginx -t 
sudo service nginx reload
```

## Автор: [Анастасия](https://github.com/PUpi-83):sunflower: