# Уменьшение образа

Всегда необходимо следить за размером образа, т.к. память и общее количество потребляемых ресурсов - всегда является критичной проблемой.

Для начала необходимо вывести все имеющиеся образы командой `docker image ls` (Если образа нет, создать при помощи команды `docker container run {container_name}`.
| REPOSITORY | TAG | IMAGE ID | CREATED | SIZE |
| ------ | ------ | ------ | ------ | ------ |
| nginx | latest | a6bd71f48f68 | 11 days ago | 187MB |

Командой `docker history nging:latest` можно вывести содержимое нашего контейнера послойно.
```sh
docker history nging:latest

IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
a6bd71f48f68   11 days ago   /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B        
<missing>      11 days ago   /bin/sh -c #(nop)  STOPSIGNAL SIGQUIT           0B        
<missing>      11 days ago   /bin/sh -c #(nop)  EXPOSE 80                    0B        
<missing>      11 days ago   /bin/sh -c #(nop)  ENTRYPOINT ["/docker-entr…   0B        
<missing>      11 days ago   /bin/sh -c #(nop) COPY file:9e3b2b63db9f8fc7…   4.62kB    
<missing>      11 days ago   /bin/sh -c #(nop) COPY file:57846632accc8975…   3.02kB    
<missing>      11 days ago   /bin/sh -c #(nop) COPY file:3b1b9915b7dd898a…   298B      
<missing>      11 days ago   /bin/sh -c #(nop) COPY file:caec368f5a54f70a…   2.12kB    
<missing>      11 days ago   /bin/sh -c #(nop) COPY file:01e75c6dd0ce317d…   1.62kB    
<missing>      11 days ago   /bin/sh -c set -x     && groupadd --system -…   112MB     
<missing>      11 days ago   /bin/sh -c #(nop)  ENV PKG_RELEASE=1~bookworm   0B        
<missing>      11 days ago   /bin/sh -c #(nop)  ENV NJS_VERSION=0.8.2        0B        
<missing>      11 days ago   /bin/sh -c #(nop)  ENV NGINX_VERSION=1.25.3     0B        
<missing>      11 days ago   /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B        
<missing>      11 days ago   /bin/sh -c #(nop)  CMD ["bash"]                 0B        
<missing>      11 days ago   /bin/sh -c #(nop) ADD file:d261a6f6921593f1e…   74.8MB  
```

## Особенности сборки пложеноий

### Пользователь не должен быть root
Особенность OC Linux в том, что каждый образ, который основывается на ней - изначально делает все действия из под пользователя root. Из-за этого при сборке необходимо использовать Обычного пользователя.
```sh
FROM alpine
WORKDIR /newuser
RUN addgroup -S newgroup && adduser -D newuser -G newgroup
RUN chown -R newuser /newuser
USER newuser
RUN whoami
```

### Использование переменных окружения

#### Dockerfile
В Dockerfile переменные окружения устанавливаются как ключ-значение. 
```sh
Посмотреть установленные переменные можно командой env.

ENV DIRNAME=/name
ENV DIRPATH=/path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```

## Практическое задание

1. Cоздайте простое приложение на flask, подключите mongodb;
2. Уменьшите размер образа;
3. Используйте пременные окружения, для коннекта к db;
4. Исключите root из Dockerfile;
5. Проверьте работоспособность сервисов.
6. Запустите сборку приложения из 1 задания, в образе Dind и опубликуите полученные образы в публичном репозитории. Разверните приложение локально.

#### 1. Создание простого приложения на Flask с подключением к MongoDB
app.py
```sh
from flask import Flask
from pymongo import MongoClient
import os

app = Flask(Proj)

mongo_host = os.environ.get('MONGO_HOST', 'localhost')
mongo_port = int(os.environ.get('MONGO_PORT', 27017))

client = MongoClient(host=mongo_host, port=mongo_port)
db = client.mydatabase

@app.route('/')
def hello():
    return f'Hello, MongoDB: {db.test_collection.find_one()["message"]}'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')

```
requirements.txt
```sh
Flask==2.1.2
pymongo==3.12.0
```

#### 2. Уменьшение размера образа
Для уменьшения размера образа можно использовать multi-stage build:
Dockerfile
```sh
FROM python:3.8 as builder

WORKDIR /app
COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.8-slim

WORKDIR /app
COPY --from=builder /usr/local/lib/python3.8/site-packages /usr/local/lib/python3.8/site-packages
COPY app.py 

RUN apt-get update && apt-get install -y gcc \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/* /var/tmp/*

USER nobody

CMD ["python", "app.py"]
```

#### 3. Использование переменных окружения для подключения к БД
В коде приложения уже используются переменные окружения для подключения к MongoDB.

#### 4. Исключение root из Dockerfile
Добавили строку USER nobody в Dockerfile.

#### 5. Проверка работоспособности сервисов
Запускаем сервис локально, предварительно установив MongoDB и проверив, что переменные окружения настроены правильно

#### 6. Сборка и публикация образа в публичном репозитории
```sh
docker build -t 111n33dh31p/flask-mongo-app

docker push 111n33dh31p/flask-mongo-app
```
