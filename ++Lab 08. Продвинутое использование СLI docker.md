# Продвинутое использование СLI docker

Создание контейнера - `docker run -itd --name alpine nginx:alpine`
Остановка контейнера - `docker pause alpine`
```sh
[node1] (local) root@192.168.0.18 ~
$ docker run -itd --name alpine nginx:alpine

Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
Digest: sha256:3923f8de8d2214b9490e68fd6ae63ea604deddd166df2755b788bef04848b9bc
Status: Downloaded newer image for nginx:alpine
83cf2788fa7b2a377670eb8718881822b4296a253a6d09ea92b6f90579a531d4

[node1] (local) root@192.168.0.18 ~
$ docker pause alpine
alpine
[node1] (local) root@192.168.0.18 ~
$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS                  PORTS     NAMES
83cf2788fa7b   nginx:alpine   "/docker-entrypoint.…"   7 seconds ago   Up 7 seconds (Paused)   80/tcp    alpine
```
Отменить приостановку контейнера.
```sh
[node1] (local) root@192.168.0.18 ~
$ docker unpause alpine
alpine
[node1] (local) root@192.168.0.18 ~
```
Команда `docker info`
```sh
[node1] (local) root@192.168.0.18 ~
$ docker info
Client:
 Version:    24.0.7
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.11.2
    Path:     /usr/local/libexec/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v2.23.0
    Path:     /usr/local/libexec/docker/cli-plugins/docker-compose
  scout: Docker Scout (Docker Inc.)
    Version:  v1.0.9
    Path:     /usr/lib/docker/cli-plugins/docker-scout

Server:
 Containers: 1
  Running: 1
  Paused: 0
  Stopped: 0
 Images: 1
 Server Version: 24.0.7
 Storage Driver: overlay2
  Backing Filesystem: xfs
  Supports d_type: true
  Using metacopy: false
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 091922f03c2762540fd057fba91260237ff86acb
 runc version: v1.1.9-0-gccaecfc
 init version: de40ad0
 Security Options:
  apparmor
  seccomp
   Profile: builtin
 Kernel Version: 4.4.0-210-generic
 Operating System: Alpine Linux v3.18 (containerized)
 OSType: linux
 Architecture: x86_64
 CPUs: 8
 Total Memory: 31.42GiB
 Name: node1
 ID: dc40ba1c-b0dd-4dec-944f-0e82d3c3962a
 Docker Root Dir: /var/lib/docker
 Debug Mode: true
  File Descriptors: 32
  Goroutines: 46
  System Time: 2023-12-02T13:51:28.797797597Z
  EventsListeners: 0
 Experimental: true
 Insecure Registries:
  127.0.0.1
  127.0.0.0/8
 Live Restore Enabled: false
 Product License: Community Engine
```

Команда `docker logs alpine` выводит все логи, которые можно фильтровать.
```sh
[node1] (local) root@192.168.0.18 ~
$ docker logs alpine
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/12/02 13:46:54 [notice] 1#1: using the "epoll" event method
2023/12/02 13:46:54 [notice] 1#1: nginx/1.25.3
2023/12/02 13:46:54 [notice] 1#1: built by gcc 12.2.1 20220924 (Alpine 12.2.1_git20220924-r10) 
2023/12/02 13:46:54 [notice] 1#1: OS: Linux 4.4.0-210-generic
2023/12/02 13:46:54 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2023/12/02 13:46:54 [notice] 1#1: start worker processes
2023/12/02 13:46:54 [notice] 1#1: start worker process 30
2023/12/02 13:46:54 [notice] 1#1: start worker process 31
2023/12/02 13:46:54 [notice] 1#1: start worker process 32
2023/12/02 13:46:54 [notice] 1#1: start worker process 33
2023/12/02 13:46:54 [notice] 1#1: start worker process 34
2023/12/02 13:46:54 [notice] 1#1: start worker process 35
2023/12/02 13:46:54 [notice] 1#1: start worker process 36
2023/12/02 13:46:54 [notice] 1#1: start worker process 37
2023/12/02 13:55:10 [notice] 37#37: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 34#34: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 30#30: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 1#1: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 36#36: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 33#33: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 32#32: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 35#35: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 31#31: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 37#37: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 35#35: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 30#30: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 31#31: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 1#1: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 34#34: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 33#33: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 32#32: signal 28 (SIGWINCH) received
2023/12/02 13:55:10 [notice] 36#36: signal 28 (SIGWINCH) received
```

```sh
[node1] (local) root@192.168.0.18 ~
$ docker attach web
2023/12/02 14:47:39 [notice] 37#37: signal 28 (SIGWINCH) received
2023/12/02 14:47:39 [notice] 36#36: signal 28 (SIGWINCH) received
2023/12/02 14:47:39 [notice] 30#30: signal 28 (SIGWINCH) received
2023/12/02 14:47:39 [notice] 1#1: signal 28 (SIGWINCH) received
2023/12/02 14:47:39 [notice] 35#35: signal 28 (SIGWINCH) received
2023/12/02 14:47:39 [notice] 33#33: signal 28 (SIGWINCH) received
```

Команда docker stats позволяет отслеживать статистику использования ресурсов контейнерами в реальном времени.
| CONTAINER ID | NAME | CPU % | MEM USAGE / LIMIT | MEM % | NET I/0 | BLOCK I/O | PIDS |
| ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| 83cf2788fa7b | nginx:alpine | 0.00% | 10.71MiB / 31.42GiB | 0.03% | 84B / 0B | 0B / 8.19kB | 9 |

#### Практическое задание
1. Запустите контейнер и посотрите информацию о состоянии docker на хосте. Выведите информацию о типе операционной системы на которой установлен docker в файл, этом используйте командку docker info.
2. Запустите произвольный контейнер. Выполните тестирование производительности в течении 3 минут. Оцените общую производительность во время выполения теста системы. Заморозьте контейнер. Разморозьте контейнер.
3. Посотрите логи веб сервера apache, когда контейнер находится под тестированием производительности.
4. Запустите еще один контейнер с mongodb. Посмотрите статистику всех конетйнеров, используя (--format), не включая столбец PIDS. Запишите все результаты в файл.
5. Изучите дополнительные параметры в тестировании производительности веб приложений.
6. Используя контейнер nginx, послитайте количество запросов к веб серверу за один день. Представьте статистику стран, с которых осуществлялись запросы на веб сервер (предванительно запустите тесты несколько раз и с разных ip).

##### 1. Информация о Docker и операционной системе
```sh
docker info > docker_info.txt
```

##### 2. Тестирование производительности контейнера
```sh
docker run -d --name stress-container stress
docker stats --no-stream stress-container
docker pause stress-container
docker unpause stress-container
```

##### 3. Просмотр логов веб-сервера Apache
```sh
docker ps
docker logs apache-container
```

##### 4. Запуск контейнера MongoDB и вывод статистики
```sh
docker run -d --name mongo-container mongo
docker stats --format "table {{.Container}}\t{{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}" $(docker ps -q) > container_stats.txt
```

##### 5. Дополнительные параметры в тестировании производительности
 - Использование опции --format для более читаемого вывода.
 - Использование флагов --format "table {{.Container}}\t{{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}" в docker stats для форматирования вывода.
##### 6. Статистика запросов к веб-серверу Nginx
```sh
docker run -d --name nginx-container -p 80:80 nginx
ab -n 1000 -c 10 http://localhost/
docker exec nginx-container cat /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c > requests_by_country.txt
```
