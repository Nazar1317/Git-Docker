# !Lab 4. Dockerfile.md

Dockerfile — это текстовый файл с инструкциями, необходимыми для создания образа контейнера. Эти инструкции включают идентификацию существующего образа, используемого в качестве основы, команды, выполняемые в процессе создания образа, и команду, которая будет выполняться при развертывании новых экземпляров этого образа контейнера.

```sh
# FROM
Задаёт родительский(базовый) образ.

# LABEL
Описывает метаданные. Например — сведения о том, кто создал и поддерживает образ.

# ENV
Устанавливает постоянные переменные среды.

#RUN
Выполняет команду и создаёт слой образа. Используется для установки в контейнер пакетов.

#COPY
Копирует в контейнер файлы и папки.

# ADD
Копирует файлы и папки в контейнер, может распаковывать локальные .tar-файлы.

# CMD
Описывает команду с аргументами, которую нужно выполнить когда контейнер будет запущен. Аргументы могут
быть переопределены при запуске контейнера. В файле может присутствовать лишь одна инструкция CMD.

# WORKDIR
Задаёт рабочую директорию для следующей инструкции.

# ARG
Задаёт переменные для передачи Docker во время сборки образа.

# ENTRYPOINT
Предоставляет команду с аргументами для вызова во время выполнения контейнера. Аргументы не переопределяются.

# EXPOSE
Указывает на необходимость открыть порт.

# VOLUME
Создаёт точку монтирования для работы с постоянным хранилищем.
```

Вот наглядый пример создания полноценного Dockerfile
```sh
FROM python:3.7.2-alpine3.8
LABEL maintainer="nazarvasilenko@gmail.com"
# Устанавливаем зависимости
RUN apk add --update git
# Задаём текущую рабочую директорию
WORKDIR /usr/src/my_app_directory
# Копируем код из локального контекста в рабочую директорию образа
COPY . .
# Задаём значение по умолчанию для переменной
ARG my_var=my_default_value
# Настраиваем команду, которая должна быть запущена в контейнере во время его выполнения
ENTRYPOINT ["python", "./app/my_script.py", "my_var"]
# Открываем порты
EXPOSE 8000
# Создаём том для хранения данных
VOLUME /my_volume
```

## Соберём образ
Наш образ будет состоять из nginx и index.html(Код в конце).

Для начала соберём наш образ
```sh
docker build . -t localhost:5000/nginx:0.0.1
```

Запустим наш образ
```sh
docker run -itd -p 8011:80 --name nginx-dock localhost:5000/nginx:0.0.1
```

Логи нашего контейнера
```sh
docker logs nginx-dock
```

Используя docker build можно автомотизировано собирать образы, которая сама будет последовательно выполнять команды.
```sh
docker build -t test/myapp.
```

## Veu.js

Установим Vite:
```sh
sudo apt install vite
```

Установим Node.js и Npm:
```sh
sudo apt install npm
```

Проиницилизируем проект на Vue.js:
```sh
npm init vite@latest testvue --template vue
# Выбираем vue ->vue
cd testvue
npm install
npm run dev
```

Cоздадим Dockerfile:
```sh
FROM node:lts-alpine as build-stage
RUN apk update && apk upgrade
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build


FROM nginx:stable-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Соберём образ и получим следующее:
```sh
# Собираем образ:
docker build -t deploytest

# Запустим образ:
docker run -itd -p 8002:80 --name deploytest deploytest 
```

Результат будет следующим:
```sh
#Удаляем все лишнее
docker system prune -a

git init
git add .
git commit -m 'deploytest'

git remote add origin git@gitlab.com:NazarVasilenko/veu-docker
git push --set-upstream origin master
```

# Практическое задание

1. Создаём одностраничный сайт, при этом пользуемся системой контроля версий Git
Структура проекта
```sh
my_website/
|-- index.html
|-- styles.css
|-- .git/
|-- .gitignore
```
index.html
```sh
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>My Dockerized Website</title>
</head>
<body>
    <div class="container">
        <h1>Hello, Docker!</h1>
        <p>This is my Dockerized single-page website.</p>
    </div>
</body>
</html>
```
styles.css
```sh
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    margin: 0;
}

.container {
    max-width: 800px;
    margin: 50px auto;
    padding: 20px;
    background-color: #fff;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    text-align: center;
}

h1 {
    color: #333;
}

p {
    color: #666;
}
```
.gitignore
```sh
node_modules/
```
Инициализация Git и коммит
```sh
cd my_website
git init
git add .
git commit -m "Initial commit"
```
2. Создаем для него Dockerfile и запускаем его
Для начала надо создать Dockerfile
```sh
# Используем базовый образ с поддержкой веб-сервера
FROM nginx:latest

# Копируем файлы веб-сайта в рабочую директорию
COPY . /usr/share/nginx/html

# Ваша директория веб-сайта
WORKDIR /usr/share/nginx/html

# Открываем порт 80
EXPOSE 80

# Команда для запуска веб-сервера
CMD ["nginx", "-g", "daemon off;"]
```

После чего - запустить
```sh
docker build -t my-website .
docker run -p 8080:80 my-website
```
Перейдя по адресу http://localhost:8080, мы увидим одностраничный веб-сайт, который теперь работает в контейнере Docker.
