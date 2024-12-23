# Volume

Для управления данными приложения, необходимо использовать память хостовой системы. Рассмотрим существующие подходы к монтированию:
 - Volume(тома) хранятся в части файловой системы хоста, управляемой Docker (/ var / lib / docker / volume / в Linux). Процессы, не относящиеся к Docker, не должны изменять эту часть файловой системы. Тома - предпочтительный способ сохранить данные в Docker.
 - Bind(привязка существующих каталогов внутрь контейнеров). Данный подход к монтированию может хранить данные в любом месте хост-системы. Процессы, не относящиеся к Docker, на хосте Docker или в контейнере Docker могут изменять их в любое время.
 - Tmpfs mounts хранятся только в временной памяти хоста и никогда не записываются в его файловую систему.

Volume можно создать явно, используя команду docker volume create. В другом случае Docker может создать том во время создания контейнера или службы.
 - Обмен данными между несколькими запущенными контейнерами. Если вы не создаете его явно, том создается при первом монтировании в контейнер. 
 - Когда хосту Docker не гарантируется наличие заданного каталога или файловой структуры. Тома помогают отделить конфигурацию хоста Docker от среды выполнения контейнера.
 - Если вы хотите хранить данные своего контейнера на удаленном хосте или у облачного провайдера, а не локально.
 - Когда вам нужно создать резервную копию, восстановить или перенести данные с одного хоста Docker на другой, тома - лучший выбор. 
 - Когда вашему приложению требуется высокопроизводительный ввод-вывод на рабочем столе Docker.
 - Когда вашему приложению требуется полностью собственное поведение файловой системы на Docker Desktop.

## Volume

Удаляем все неиспользуемые Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas# 
```

Создаем Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas# docker volume create storage
storage
root@Ubunty-22:/home/nazarvas# docker volume create db
db
root@Ubunty-22:/home/nazarvas#
```
Список Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume ls
DRIVER    VOLUME NAME
local     db
local     storage
root@Ubunty-22:/home/nazarvas#
```

### Пример №1 (Какие Volume содержатся в стандартном образе MongoDB.

Скачиваем образ с MongoDB
```sh
root@Ubunty-22:/home/nazarvas# docker image pull mongo
Using default tag: latest
latest: Pulling from library/mongo
cbe3537751ce: Pull complete 
a80d99d2ce19: Pull complete 
cdb44dc221f3: Pull complete 
52cece2eeeb6: Pull complete 
9484737e86c4: Pull complete 
43ad935b75c0: Pull complete 
a3ac6a8edff6: Pull complete 
90580617c703: Pull complete 
3c932f959341: Pull complete 
Digest: sha256:4301b0558d8170db609563a747d0de57fc02f94eabb7cf08869b418dfcd1ead8
Status: Downloaded newer image for mongo:latest
docker.io/library/mongo:latest
```
Вся информация о скаченом образе.
```sh
root@Ubunty-22:/home/nazarvas# docker image inspect mongo
[
    {
        "Id": "sha256:5acb2131d51f8ae39cbf4360b2f3f3e6d78dd14797bea477e173bb3c0e1abca7",
        "RepoTags": [
            "mongo:latest"
        ],
        "RepoDigests": [

            "mongo@sha256:4301b0558d8170db609563a747d0de57fc02f94eabb7cf08869b418dfcd1ead8"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2023-12-02T04:58:55.267398258Z",
        "Container": "4d365bd2e8d1d53189c05e74c0a310ad2536ce9bd2afb1b0f8179cb9896b995e",
        "ContainerConfig": {
            "Hostname": "4d365bd2e8d1",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "27017/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.16",
                "JSYAML_VERSION=3.13.1",
                "MONGO_PACKAGE=mongodb-org",
                "MONGO_REPO=repo.mongodb.org",
                "MONGO_MAJOR=7.0",
                "MONGO_VERSION=7.0.4",
                "HOME=/data/db"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"mongod\"]"
            ],
            "Image": "sha256:97bec03e336f9cdfc22406f723a076dc465c5dceb3cb15bec75cbe3be57e9c66",
            "Volumes": {
                "/data/configdb": {},
                "/data/db": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "22.04"
            }
        },
        "DockerVersion": "20.10.23",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "27017/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.16",
                "JSYAML_VERSION=3.13.1",
                "MONGO_PACKAGE=mongodb-org",
                "MONGO_REPO=repo.mongodb.org",
                "MONGO_MAJOR=7.0",
                "MONGO_VERSION=7.0.4",
                "HOME=/data/db"
            ],
            "Cmd": [
                "mongod"
            ],
            "Image": "sha256:97bec03e336f9cdfc22406f723a076dc465c5dceb3cb15bec75cbe3be57e9c66",
            "Volumes": {
                "/data/configdb": {},
                "/data/db": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "22.04"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 757308229,
        "VirtualSize": 757308229,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/3af1a1c6a96c3db5415c2f31b2825d02b5ff6a4e7798290f0a8cd926a0422515/diff:/var/lib/docker/overlay2/f2aaf0b615ce98bd22cc970ace7580ba9773ff3a49be362b5a93045711805110/diff:/var/lib/docker/overlay2/cef192240329a6166e70db9e014ab2374a719af25e8691d41d36c6adb9a40858/diff:/var/lib/docker/overlay2/5a93f7dbd3552c6115275bc18d3a1c52b79cb510e977e066742026a2e0d72a2a/diff:/var/lib/docker/overlay2/677ce927b597343cffc86110a24417c3502861db87246a95a1d0f283b79fc1ee/diff:/var/lib/docker/overlay2/efb6c9a163193a9781740b5b27ce5d1d94f2c0ea8ee3a69def8a7482e7304134/diff:/var/lib/docker/overlay2/f8ba177f1a6628022a52b8f713aaf861a54ec1f4e693ae13edac736493e755b0/diff:/var/lib/docker/overlay2/c1d12f5c3584463414435c9cdb555ad9956003735e4584326b749eb7d40a2e71/diff",
                "MergedDir": "/var/lib/docker/overlay2/f9a25f149b6e891d4e2726e30bf4a1669aedd106e5cb110056579e307dcaedc1/merged",
                "UpperDir": "/var/lib/docker/overlay2/f9a25f149b6e891d4e2726e30bf4a1669aedd106e5cb110056579e307dcaedc1/diff",
                "WorkDir": "/var/lib/docker/overlay2/f9a25f149b6e891d4e2726e30bf4a1669aedd106e5cb110056579e307dcaedc1/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:8ceb9643fb36a8ac65882c07e7b2fff9fd117673d6784221a83d3ad076a9733e",
                "sha256:31ab4ed7865b6bb8fe11bb1e3adf04a36316fb62a6689351f28b198220a5ae5e",
                "sha256:4afa0daded15982086ba42c4e69cc2100cd6161499c6ee16aa11522aad0bf0e0",
                "sha256:b7b89cbd38fc9137df92fba05bee315146b2cf49dc017c4959d7e75fb2ffee79",
                "sha256:96d6b18480dff561faea604527c15fce559d7f08c6da0b929b3818988630ec7d",
                "sha256:41fb075b994993168e411d0a98ac4a37bea47431cb6fadddfecf80f6da6dd06d",
                "sha256:46c2d8fbe3ff52d4479aa5e11bc83f4e6db83f0b7d6e0a9b0e34624240050793",
                "sha256:becbd90af528c5a05fd6017dad68a5a605926fb51f3e19c30284e8962d730da0",
                "sha256:e1e99cd4802850735b54bbddb72e42a691cb324a0c08067f8f7673177178b356"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]


```

### Пример №2 (Создание Volume).

Создаем Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas# docker volume create storage
storage
root@Ubunty-22:/home/nazarvas# docker volume create db
db
root@Ubunty-22:/home/nazarvas#
```
Смотрим список Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume ls
DRIVER    VOLUME NAME
local     db
local     storage
root@Ubunty-22:/home/nazarvas# 
```
Запустим контейнер с монго
```sh
root@Ubunty-22:/home/nazarvas# docker container run -d -v storage:/data/db --name mongo_test mongo
991037e82a7cce749db9bb9abb55b3d6acbc242d29aad88ea7c68df5a0fd51d8
root@Ubunty-22:/home/nazarvas# 
```
Список всех Volume с дополнительным Volume.
```sh
oot@Ubunty-22:/home/nazarvas# docker volume ls
DRIVER    VOLUME NAME
local     c9483038f2274bd84a425949e9562adfca2bfd1dadde145b54c9f00118c9c80f
local     db
local     storage
root@Ubunty-22:/home/nazarvas# 
```
Проинспектируем и видим, что Volume прикручен к контейнеру
```sh
"Mounts": [
            {
                "Type": "volume",
                "Name": "storage",
                "Source": "/var/lib/docker/volumes/storage/_data",
                "Destination": "/data/db",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "c9483038f2274bd84a425949e9562adfca2bfd1dadde145b54c9f00118c9c80f",
                "Source": "/var/lib/docker/volumes/c9483038f2274bd84a425949e9562adfca2bfd1dadde145b54c9f00118c9c80f/_data",
                "Destination": "/data/configdb",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
```

### Пример №3. (Правильное монтирование)
Сперва останавливаем контейнер, а потом уже удаляем
```sh
root@Ubunty-22:/home/nazarvas# docker container stop mongo_test
mongo_test
root@Ubunty-22:/home/nazarvas# docker container rm mongo_test
mongo_test
root@Ubunty-22:/home/nazarvas# 
```
Создаём Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume create config
config
root@Ubunty-22:/home/nazarvas# 
```
Запустим контейнер, прикрутим Volume и пробросим порты
```sh
root@Ubunty-22:/home/nazarvas# docker container run -d -v storage:/data/db -v config:/data/configdb -p 27018:27017 --name mongo_test mongo
78d297978ec5c0aa81561acf88b0c2eafbf88ba19ccf48a66cb75acf6b3f6d5b
root@Ubunty-22:/home/nazarvas# 
```
Проинспектируем и видим, что ручные Volume - внутри контейнера
```sh
"Config": {
            "Hostname": "78d297978ec5",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "27017/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.16",
                "JSYAML_VERSION=3.13.1",
                "MONGO_PACKAGE=mongodb-org",
                "MONGO_REPO=repo.mongodb.org",
                "MONGO_MAJOR=7.0",
                "MONGO_VERSION=7.0.4",
                "HOME=/data/db"
            ],
            "Cmd": [
                "mongod"
            ],
            "Image": "mongo",
            "Volumes": {
                "/data/configdb": {},
                "/data/db": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "22.04"
            }
        },
```

### Пример №4. (Примонтируем Volume к другому контейнеру)
Удаляем все неиспользуемые Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas#
```
Запустим вторую Mongo
```sh
root@Ubunty-22:/home/nazarvas# docker container run -d -v storage:/data/db -v config:/data/configdb -p 27019:27017 --name mongo_test1 mongo
284016fa210388fc66e38b0cfb402cf3e34dda8b2f42293619bcf96fa59d6552
root@Ubunty-22:/home/nazarvas#
```
Просмотрим запущенные контейнеры
```sh
root@Ubunty-22:/home/nazarvas# docker container ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                           NAMES
78d297978ec5   mongo     "docker-entrypoint.s…"   6 minutes ago   Up 6 minutes   0.0.0.0:27018->27017/tcp, :::27018->27017/tcp   mongo_test
root@Ubunty-22:/home/nazarvas# 
```

### Пример №5. (Установка label label механизм применения метаданных к объектам Docker)
Удаляем все неиспользуемые volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas#
```
Создаём Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume create --name storage --label maintainer='Nazar.' --label used_for='mongo db storage'
storage
root@Ubunty-22:/home/nazarvas#
```
Просмотрим установленные label
```sh
root@Ubunty-22:/home/nazarvas# docker volume inspect storage
[
    {
        "CreatedAt": "2023-12-15T13:12:40+03:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/storage/_data",
        "Name": "storage",
        "Options": null,
        "Scope": "local"
    }
]
root@Ubunty-22:/home/nazarvas# 
```

### Пример №6. (Манипулирование данными)
Удаляем все неиспользуемые volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas#
```
Создадим драйвер
```sh
root@Ubunty-22:/home/nazarvas# docker volume create --name storage
storage
root@Ubunty-22:/home/nazarvas#
```
Запустим контейнер
```sh
root@Ubunty-22:/home/nazarvas# docker container run -d -v storage:/data/db -v config:/data/configdb -p 27018:27017 --name mongo_testik mongo
4e2a84e9c4d86d8f2a74e0850a3f6c5a1db2fde8bf895ede2f75b9dc14049344
root@Ubunty-22:/home/nazarvas# 
```
Проинспектируем
```sh
root@Ubunty-22:/home/nazarvas# docker volume inspect storage
[
    {
        "CreatedAt": "2023-12-15T13:12:40+03:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/storage/_data",
        "Name": "storage",
        "Options": null,
        "Scope": "local"
    }
]
root@Ubunty-22:/home/nazarvas# 
```

### Пример №7. (Создание Volume с опциями)
Удаляем все неиспользуемые volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas#
```
Создаём Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume create --driver local \
    --opt type=tmpfs \
    --opt device=tmpfs \
    --opt o=size=100m \
    storage
storage
root@Ubunty-22:/home/nazarvas#
```
Запустим контейнер
```sh
root@Ubunty-22:/home/nazarvas# docker container run -d -v storage:/data/db --name mongo mongo
3017cf71b0476132bc5f03888c86d1b7e156407b15e562bbdd63a7fd77b164c0
root@Ubunty-22:/home/nazarvas#
```

### Пример №8. (Примоонтируем volume при помощи --mount)
Удаляем контейнер
```sh
root@Ubunty-22:/home/nazarvas# docker container rm -f webhost
webhost
root@Ubunty-22:/home/nazarvas#
```
Удаляем все неиспользуемые volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas#
```
Запускаем контейнер
```sh
root@Ubunty-22:/home/nazarvas# docker container run -d --mount source=storage,target=/usr/share/nginx/html --name webhost nginx:alpine
Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
c926b61bad3b: Pull complete 
eb2797aa8e79: Pull complete 
47df6ca4b6bc: Pull complete 
5ea1ba8ab969: Pull complete 
6a4b140a5e7c: Pull complete 
c99555e79d52: Pull complete 
f9302969eafd: Pull complete 
d7fb62c2e1cc: Pull complete 
Digest: sha256:3923f8de8d2214b9490e68fd6ae63ea604deddd166df2755b788bef04848b9bc
Status: Downloaded newer image for nginx:alpine
2d6bceece6d95f54c4b2071f4db438fb500af55fa95dac3ad17c122f1868ee58
root@Ubunty-22:/home/nazarvas#
```

### Пример №9. (Монтирование каталога файловой системы хоста в режиме только для чтения)
Удаляем все не используемые volume
```sh
root@Ubunty-22:/home/nazarvas# docker container rm -f webhost
webhost
root@Ubunty-22:/home/nazarvas#
```
### Пример №10. (Пример монтирования нескольки файлов)
Удаляем контейнер
```sh
root@Ubunty-22:/home/nazarvas# docker container rm -f webhost
webhost
root@Ubunty-22:/home/nazarvas# 
```
Удаляем все неиспользуемые volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas#
```
Запускаем контейнер с параметрами
```sh
docker container run -d \
    --mount type=bind,source="$(pwd)"/index.html,target=/usr/share/nginx/html/index.html \
    --mount type=bind,source="$(pwd)"/about.html,target=/usr/share/nginx/html/about.html \
    --publish 80:80 \
    --name webhost nginx:alpine
```
### Пример №11. (Пример создания tmpfs)
Удаляем контейнер
```sh
root@Ubunty-22:/home/nazarvas# docker container rm -f webhost
webhost
root@Ubunty-22:/home/nazarvas# 
```
Удаляем все не используемые Volume
```sh
root@Ubunty-22:/home/nazarvas# docker volume prune -f
Total reclaimed space: 0B
root@Ubunty-22:/home/nazarvas#
```
Запускаем контейнер
```sh
root@Ubunty-22:/home/nazarvas# docker run -d \
  -it \
  --name webhost \
  --mount type=tmpfs,destination=/app,tmpfs-size=100m \
  nginx:alpine  
9817ae3545c3bc146d6b322583c7a156e1f987f0eaa4ca52c8f7be2e14368ff1
root@Ubunty-22:/home/nazarvas#
```

# Практическое задание
1. Примонтируйте volume в проект
```sh
# Создаем директорию
mkdir my_volume

# Монтируем volume в контейнер
docker run -d -v $(pwd)/my_volume:/app/data --name volume_example nginx
```
2. Повторите примеры для СУБД PostgreSQL
```sh
docker run -d --name postgres_container -e POSTGRES_PASSWORD=mysecretpassword -p 5432:5432 postgres

# Подключение к контейнеру
docker exec -it postgres_container psql -U postgres

# Внутри PostgreSQL выполните SQL-запросы для создания таблиц
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL
);

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),
    product_name VARCHAR(100) NOT NULL,
    quantity INT NOT NULL
);

\q  # выход
```
3. Проинспектируйте Docker контейнер из примера 12 на наличие volume
```sh
docker inspect container_id
```
4. Выполните ПРИМЕР #6 для Nginx
```sh
# Используем базовый образ Nginx
FROM nginx:latest

# Копируем конфигурационный файл внутрь контейнера
COPY nginx.conf /etc/nginx/nginx.conf

# Копируем стартовую страницу
COPY index.html /usr/share/nginx/html/

# Открываем порт 8080
EXPOSE 8080
```
Теперь создаём файл nginx.conf с кастомной конфигурацией Nginx
```sh
server {
    listen 8080;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
```
Создаём index.html с измененным контентом, после чего
```sh
docker build -t custom_nginx .
docker run -d -p 8080:8080 --name my_nginx custom_nginx
```
Проверяет работоспособность `curl`
```sh
curl http://localhost:8080
```
