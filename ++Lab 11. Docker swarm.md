# Lab 11. Docker swarm.md
#### Уменьшаем размер образа
Docker Swarm (или режим роя Docker) — это инструмент оркестрации контейнеров, упрощенный аналог Kubernetes. Он позволяет управлять несколькими контейнерами, развернутыми на нескольких хостах, на которых запущен Docker.
TCP-порт 2377 для управления кластером.
Порт TCP и UDP 7946 для связи между узлами
UDP-порт 4789 для оверлейного сетевого трафика

##### Инициализация кластера Swarm
Для создания кластера выберем систему, которая будет являться управляемым manager узлом и выполним команду:
```sh
docker swarm init --advertise-addr
```
В выводе наблюдаем сгенерированную команду, позволяющую инициализировать worker узел
```sh
root@Ubunty-22:/home/nazarvas# docker swarm init --advertise-addr 192.168.0.8
Swarm initialized: current node (tmdjqvlyle31fm9kkyxgesnkp) is now a manager.
To add a worker to this swarm, run the following command:
    docker swarm join --token SWMTKN-1-4ws8lcqpfmvrd8zyisuqpl7fvpmj5hq5i39l3l2hv39pca5rha-47i4n54mq6b30ji65sq52q8f8 192.168.0.8:2377
To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
root@Ubunty-22:/home/nazarvas# 
```
Выполним следующую команду для повторного получения токена
```sh
root@Ubunty-22:/home/nazarvas# docker swarm join-token worker
To add a worker to this swarm, run the following command:
    docker swarm join --token SWMTKN-1-4ws8lcqpfmvrd8zyisuqpl7fvpmj5hq5i39l3l2hv39pca5rha-47i4n54mq6b30ji65sq52q8f8 192.168.0.8:2377
root@Ubunty-22:/home/nazarvas#
```
Посмотрим состав узлов кластера
```sh
root@Ubunty-22:/home/nazarvas# docker node ls
ID                            HOSTNAME    STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
tmdjqvlyle31fm9kkyxgesnkp *   Ubunty-22   Ready     Active         Leader           20.10.24
root@Ubunty-22:/home/nazarvas#
```
Посмотрим текущий статус состояния кластера
```sh
root@Ubunty-22:/home/nazarvas# docker info
Client: Docker Engine - Community
 Version:    24.0.7
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.11.2
    Path:     /usr/libexec/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v2.21.0
    Path:     /usr/libexec/docker/cli-plugins/docker-compose

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 2
 Server Version: 20.10.24
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: active
  NodeID: tmdjqvlyle31fm9kkyxgesnkp
  Is Manager: true
  ClusterID: 6yfw7e8klmjvfxc0efmx8mz4p
  Managers: 1
  Nodes: 1
  Default Address Pool: 10.0.0.0/8  
  SubnetSize: 24
  Data Path Port: 4789
  Orchestration:
   Task History Retention Limit: 5
  Raft:
   Snapshot Interval: 10000
   Number of Old Snapshots to Retain: 0
   Heartbeat Tick: 1
   Election Tick: 10
  Dispatcher:
   Heartbeat Period: 5 seconds
  CA Configuration:
   Expiry Duration: 3 months
   Force Rotate: 0
  Autolock Managers: false
  Root Rotation In Progress: false
  Node Address: 192.168.0.8
  Manager Addresses:
   192.168.0.8:2377
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 2806fc1057397dbaeefbea0e4e17bddfbd388f38
 runc version: 
 init version: de40ad0
 Security Options:
  apparmor
  seccomp
   Profile: default
  cgroupns
 Kernel Version: 6.2.0-39-generic
 Operating System: Ubuntu Core 22
 OSType: linux
 Architecture: x86_64
 CPUs: 2
 Total Memory: 1.913GiB
 Name: Ubunty-22
 ID: GMNK:PYUR:AEJZ:HCUU:GJKQ:MWEG:2RZ7:Z7JK:JFVY:HGCS:3K65:TWBR
 Docker Root Dir: /var/snap/docker/common/var-lib-docker
 Debug Mode: false
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false
root@Ubunty-22:/home/nazarvas#
```
Удалим ноду из кластера
```sh
docker swarm leave
```
Удалим ноду на упраляющем узле
```sh
docker swarm rm node_name
```

##### Развертывание сервиса
Создаём сервис
```sh
root@Ubunty-22:/home/nazarvas# $docker service inspect helloworld1
inspect: unrecognized service
root@Ubunty-22:/home/nazarvas# docker service create --replicas 1 --name helloworld1 alpine ping vk.com
hy2a27ji2jgyvu20uwjxn0s0u
overall progress: 1 out of 1 tasks 
1/1: running   
verify: Service converged 
root@Ubunty-22:/home/nazarvas# 
```
Просмотрим все существующие сервисы
```sh
root@Ubunty-22:/home/nazarvas# docker service ls
ID             NAME          MODE         REPLICAS   IMAGE           PORTS
hy2a27ji2jgy   helloworld1   replicated   1/1        alpine:latest   
root@Ubunty-22:/home/nazarvas#
```

```sh
root@Ubunty-22:/home/nazarvas# docker service inspect helloworld1
[
    {
        "ID": "hy2a27ji2jgyvu20uwjxn0s0u",
        "Version": {
            "Index": 11
        },
        "CreatedAt": "2023-12-15T12:22:33.268599571Z",
        "UpdatedAt": "2023-12-15T12:22:33.268599571Z",
        "Spec": {
            "Name": "helloworld1",
            "Labels": {},
            "TaskTemplate": {
                "ContainerSpec": {
                    "Image": "alpine:latest@sha256:51b67269f354137895d43f3b3d810bfacd3945438e94dc5ac55fdac340352f48",
                    "Args": [
                        "ping",
                        "vk.com"
                    ],
                    "Init": false,
                    "StopGracePeriod": 10000000000,
                    "DNSConfig": {},
                    "Isolation": "default"
                },
                "Resources": {
                    "Limits": {},
                    "Reservations": {}
                },
                "RestartPolicy": {
                    "Condition": "any",
                    "Delay": 5000000000,
                    "MaxAttempts": 0
                },
                "Placement": {
                    "Platforms": [
                        {
                            "Architecture": "amd64",
                            "OS": "linux"
                        },
                        {
                            "OS": "linux"
                        },
                        {
                            "OS": "linux"
                        },
                        {
                            "Architecture": "arm64",
                            "OS": "linux"
                        },
                        {
                            "Architecture": "386",
                            "OS": "linux"
                        },
                        {
                            "Architecture": "ppc64le",
                            "OS": "linux"
                        },
                        {
                            "Architecture": "s390x",
                            "OS": "linux"
                        }
                    ]
                },
                "ForceUpdate": 0,
                "Runtime": "container"
            },

            "Mode": {
                "Replicated": {
                    "Replicas": 1
                }
            },
            "UpdateConfig": {
                "Parallelism": 1,
                "FailureAction": "pause",
                "Monitor": 5000000000,
                "MaxFailureRatio": 0,
                "Order": "stop-first"
            },
            "RollbackConfig": {
                "Parallelism": 1,
                "FailureAction": "pause",
                "Monitor": 5000000000,
                "MaxFailureRatio": 0,
                "Order": "stop-first"
            },
            "EndpointSpec": {
                "Mode": "vip"
            }
        },
        "Endpoint": {
            "Spec": {}
        }
    }
]
root@Ubunty-22:/home/nazarvas#
```
Посмотрим информацию на каких узлах развернут сервис
```sh
root@Ubunty-22:/home/nazarvas# docker service ps helloworld1
ID             NAME            IMAGE           NODE        DESIRED STATE   CURRENT STATE           ERROR     PORTS
4112c6601mvj   helloworld1.1   alpine:latest   Ubunty-22   Running         Running 4 minutes ago             
root@Ubunty-22:/home/nazarvas# 
```
Детальная информация
```sh
root@Ubunty-22:/home/nazarvas# docker ps
CONTAINER ID   IMAGE           COMMAND         CREATED         STATUS         PORTS     NAMES
a0a2c8e402ee   alpine:latest   "ping vk.com"   5 minutes ago   Up 5 minutes             helloworld1.1.4112c6601mvjcln7y6gjogedh
root@Ubunty-22:/home/nazarvas#
```
Удалим серввис
```sh
root@Ubunty-22:/home/nazarvas# docker service rm helloworld1
helloworld1
root@Ubunty-22:/home/nazarvas#

root@Ubunty-22:/home/nazarvas# docker service ps helloworld1
no such service: helloworld1
root@Ubunty-22:/home/nazarvas#
```

##### Масштабирование сервиса и обновление версии
Создадим новый сервис с одной репликой
```sh
root@Ubunty-22:/home/nazarvas# docker service create --replicas 1 --name whoami containous/whoami
yuq74cikorlthj0k7rtmd8rtq
overall progress: 1 out of 1 tasks 
1/1: running   
verify: Service converged 
root@Ubunty-22:/home/nazarvas# 
```
Увеличим количество реплик до 5
```sh
root@Ubunty-22:/home/nazarvas# docker service scale whoami scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   
2/5: running   
3/5: running   
4/5: running   
5/5: running   
verify: Service converged 
root@Ubunty-22:/home/nazarvas#
```
Информация о распределении контейнеров по узлам
```sh
root@Ubunty-22:/home/nazarvas# docker service ps whoami
ID             NAME       IMAGE                      NODE        DESIRED STATE   CURRENT STATE            ERROR     PORTS
x0jlrx1ubptd   whoami.1   containous/whoami:latest   Ubunty-22   Running         Running 49 minutes ago             
e4na597oshd5   whoami.2   containous/whoami:latest   Ubunty-22   Running         Running 48 minutes ago             
0vjjyy6vt1o9   whoami.3   containous/whoami:latest   Ubunty-22   Running         Running 48 minutes ago             
8q3pwlxn7tgh   whoami.4   containous/whoami:latest   Ubunty-22   Running         Running 48 minutes ago             
g1m0afaxbifc   whoami.5   containous/whoami:latest   Ubunty-22   Running         Running 48 minutes ago             
root@Ubunty-22:/home/nazarvas# 
```
Понижаем количество реплик до 3х
```sh
root@Ubunty-22:/home/nazarvas# docker service scale whoami=3
whoami scaled to 3
overall progress: 3 out of 3 tasks 
1/3: running   
2/3: running   
3/3: running   
verify: Service converged 
root@Ubunty-22:/home/nazarvas#
```
Удалим сервис
```sh
root@Ubunty-22:/home/nazarvas# docker service rm whoami
whoami
root@Ubunty-22:/home/nazarvas#
```
Снова разворачиваем
```sh
root@Ubunty-22:/home/nazarvas# docker service create --replicas 5 -p 80:80 --name whoami containous/whoami
e1xs7x8ktwjsxffdaoqnwn1el
overall progress: 5 out of 5 tasks 
1/5: running   
2/5: running   
3/5: running   
4/5: running   
5/5: running   
verify: Service converged 
root@Ubunty-22:/home/nazarvas#
```
Решим задачу обновления контейнера, при работе предыдущей версии приложения
```sh
root@Ubunty-22:/home/nazarvas# docker service update --image stefanscherer/whoami whoami
whoami
overall progress: 5 out of 5 tasks 
1/5: running   
2/5: running   
3/5: running   
4/5: running   
5/5: running   
verify: Service converged 
root@Ubunty-22:/home/nazarvas#
```
Удаляем старые порты и добавляем новые
```sh
root@Ubunty-22:/home/nazarvas# docker service update --image stefanscherer/whoami --publish-rm published=80,target=80  --publish-add published=80,target=8080 whoami
whoami
overall progress: 5 out of 5 tasks 
1/5: running   
2/5: running   
3/5: running   
4/5: running   
5/5: running   
verify: Service converged 
root@Ubunty-22:/home/nazarvas#
```

## Практические задания
1.Выполняем установку кластера с одной управляющей и двумя рабочими нодами.
На одном из узлов выполняем инициализацию Swarm
```sh
docker swarm init --advertise-addr eth0
docker swarm join
```
2. Удаление и повторное добавление рабочей ноды
Выбираем узел, который хотим удалить и выполняем
```sh
docker swarm leave
```
3. Создание Docker-образа с метаданными узла
Создаем простое приложение на языке JS(к примеру), которое выводит метаданные узла, включая IP.
```sh
server.js
-------------------------
const express = require('express');
const os = require('os');

const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send(`Node IP: ${os.networkInterfaces().eth0[0].address}\n`);
});

app.listen(port, () => {
  console.log(`Server running on http://localhost:${port}`);
});
```
```sh
Dockerfile:
-------------------------
ROM node:14

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```
После чего необходимо собрать образ
```sh
docker build -t metadata-app .
```
4. Развёртывание сервиса с 3 репликами
```sh
docker service create --replicas 3 -p 3000:3000 --name metadata-service metadata-app
```
5. Пересборка образа с дополнительной информацией о разработчике
Обновляем код в 'server.js' добавив информацию о разработчике и пересобираем образ
```sh
server.js
-------------------------
const express = require('express');
const os = require('os');

const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send(`Node IP: ${os.networkInterfaces().eth0[0].address}\nDeveloper: Your Name\n`);
});

app.listen(port, () => {
  console.log(`Server running on http://localhost:${port}`);
});
```
Пересобираем образ
```sh
docker build -t metadata-app .
```
Обновляем сервис
```sh
docker service update --image metadata-app metadata-service
```
