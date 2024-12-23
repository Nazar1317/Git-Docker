
# Установка Minikube
### Установка kubectl
```sh
###################################################################################################################################
sudo apt-get update && sudo apt-get install -y apt-transport-https
###################################################################################################################################
root@Ubunty-22:/home/nazarvas# sudo apt-get update && sudo apt-get install -y apt-transport-https
Hit:1 http://ru.archive.ubuntu.com/ubuntu jammy InRelease                                        
Hit:2 https://download.docker.com/linux/ubuntu jammy InRelease                                   
Get:3 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:4 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1 051 kB]
Get:5 http://ru.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Hit:6 http://ru.archive.ubuntu.com/ubuntu jammy-backports InRelease     
Get:7 http://ru.archive.ubuntu.com/ubuntu jammy-updates/main i386 Packages [547 kB]
Get:8 http://ru.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [1 263 kB]
Get:9 http://security.ubuntu.com/ubuntu jammy-security/main i386 Packages [383 kB]              
Get:10 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [823 kB]
Get:11 http://ru.archive.ubuntu.com/ubuntu jammy-updates/universe i386 Packages [675 kB]
Get:12 http://ru.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1 020 kB]                              
Get:13 http://security.ubuntu.com/ubuntu jammy-security/universe i386 Packages [580 kB]                                  
Fetched 6 571 kB in 7s (881 kB/s)                                                                                        
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
apt-transport-https is already the newest version (2.4.11).
0 upgraded, 0 newly installed, 0 to remove and 8 not upgraded.
root@Ubunty-22:/home/nazarvas#

###################################################################################################################################
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
###################################################################################################################################
root@Ubunty-22:/home/nazarvas# curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).
OK
root@Ubunty-22:/home/nazarvas#

###################################################################################################################################
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
###################################################################################################################################
root@Ubunty-22:/home/nazarvas# echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
root@Ubunty-22:/home/nazarvas#

###################################################################################################################################
sudo apt-get update
###################################################################################################################################
root@Ubunty-22:/home/nazarvas# sudo apt-get update
Hit:1 http://security.ubuntu.com/ubuntu jammy-security InRelease                                                         
Hit:2 https://download.docker.com/linux/ubuntu jammy InRelease                                                           
Hit:4 http://ru.archive.ubuntu.com/ubuntu jammy InRelease                                           
Get:3 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [8 993 B]
Hit:5 http://ru.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:6 http://ru.archive.ubuntu.com/ubuntu jammy-backports InRelease
Get:7 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 Packages [69,9 kB]
Fetched 78,9 kB in 3s (31,4 kB/s)    
Reading package lists... Done
W: https://apt.kubernetes.io/dists/kubernetes-xenial/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.
root@Ubunty-22:/home/nazarvas#

###################################################################################################################################
sudo apt-get install -y kubectl
###################################################################################################################################
```

### Установка Minikube
root@Ubunty-22:/home/nazarvas# curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
> curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube
```sh
|  % Total  | % Received | % Xferd | Average |  Speed |   Time  |  Time   |   Time  | Current |
| --------- | ---------- | ------- | ------- | ------ | ------- | ------- | ------- | ------- |
| --------- | ---------- | ------- |  Dload  | Upload |  Total  |  Spent  |   Left  |  Speed  |
|  34 89.3M |  34 30.7M  |    0    |  1316k  |    0   | 0:01:09 | 0:00:23 | 0:00:46 |  2048k  |
```

root@Ubunty-22:/home/nazarvas# sudo mkdir -p /usr/local/bin/
root@Ubunty-22:/home/nazarvas# sudo install minikube /usr/local/bin/

Чтобы убедиться в том, что гипервизор и Minikube были установлены корректно, выполняем следующую команду, которая запускает локальный кластер Kubernetes:

#### VirutalBox
```sh
root@Ubunty-22:/home/nazarvas# minikube start --driver=virtualbox   
😄  minikube v1.32.0 on Ubuntu 22.04 (vbox/amd64)   
✨  Using the virtualbox driver based on user configuration   
🤷  Exiting due to PROVIDER_VIRTUALBOX_NOT_FOUND: The 'virtualbox' provider was not found: unable to find VBoxManage in $PATH   
💡  Suggestion: Install VirtualBox
📘  Documentation: https://minikube.sigs.k8s.io/docs/reference/drivers/virtualbox/
```

#### Docker
```sh
root@Ubunty-22:/home/nazarvas# minikube start --driver=docker
😄  minikube v1.32.0 on Ubuntu 22.04 (vbox/amd64)
✨  Using the docker driver based on user configuration
🛑  The "docker" driver should not be used with root privileges. If you wish to continue as root, use --force.
💡  If you are running minikube within a VM, consider using --driver=none:
📘    https://minikube.sigs.k8s.io/docs/reference/drivers/none/
```

`❌  Exiting due to DRV_AS_ROOT: The "docker" driver should not be used with root privileges.`
`###################################################################################################################################`
Решение следующее:   
```sh
minikube start --driver=none
```

`❌  Exiting due to GUEST_MISSING_CONNTRACK: Sorry, Kubernetes 1.28.3 requires conntrack to be installed in root's path`
`###################################################################################################################################`
Решение следующее:   
```sh
sudo apt update && sudo apt upgrade
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.26.0/crictl-v1.26.0-linux-amd64.tar.gz
minikube start docker --force
```
Запустим minikube:
```sh
root@Ubunty-22:/home/nazarvas# minikube start docker --force
😄  minikube v1.32.0 on Ubuntu 22.04 (vbox/amd64)
❗  minikube skips various validations when --force is supplied; this may lead to unexpected behavior
✨  Automatically selected the docker driver. Other choices: none, ssh
🛑  The "docker" driver should not be used with root privileges. If you wish to continue as root, use --force.
💡  If you are running minikube within a VM, consider using --driver=none:
📘    https://minikube.sigs.k8s.io/docs/reference/drivers/none/
🧯  The requested memory allocation of 1959MiB does not leave room for system overhead (total system memory: 1959MiB). You may face stability issues.
💡  Suggestion: Start minikube with less memory allocated: 'minikube start --memory=1959mb'
📌  Using Docker driver with root privileges
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.28.3 preload ...
    > gcr.io/k8s-minikube/kicbase...:  453.90 MiB / 453.90 MiB  100.00% 6.09 Mi
    > preloaded-images-k8s-v18-v1...:  403.35 MiB / 403.35 MiB  100.00% 5.11 Mi
🔥  Creating docker container (CPUs=2, Memory=1959MB) ...
🐳  Preparing Kubernetes v1.28.3 on Docker 24.0.7 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
❗  Executing "docker container inspect minikube --format={{.State.Status}}" took an unusually long time: 7.204066296s
💡  Restarting the docker service may improve performance.
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
root@Ubunty-22:/home/nazarvas# 
```


Убедимся что minikube запущен:
```sh
root@Ubunty-22:/home/nazarvas# minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
root@Ubunty-22:/home/nazarvas#
```

Если же minikube был запущен без ключа(--vm-driver), то следует пересоздать его, при помощи:
```sh
minikube stop
minikube delete
minikube start --driver=virtualbox
```

### Краткое знакомство с кластером
Создаём развёртывание в Kubernetes, использую образ echoserver(простой HTTP-сервер)
```sh
root@Ubunty-22:/home/nazarvas# kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.10
deployment.apps/hello-minikube created
root@Ubunty-22:/home/nazarvas#
```

Проверим, что всё работает
```sh
root@Ubunty-22:/home/nazarvas# kubectl get pod
|               NAME              | READY |  STATUS  | RESTARTS | AGE |        
| ------------------------------- | ----- | -------- | -------- | --- |
| hello-minikube-77d966488c-fnfhf |  1/1  | Running  |     0    | 80s |
root@Ubunty-22:/home/nazarvas#
```

Для получения доступа к объекту извне, создаём объект сервиса
```sh
root@Ubunty-22:/home/nazarvas# kubectl expose deployment hello-minikube --type=NodePort --port=8080
service/hello-minikube exposed
root@Ubunty-22:/home/nazarvas# 
```
Узнаем URL адрес сервиса
```sh
root@Ubunty-22:/home/nazarvas# minikube service hello-minikube --url
http://192.168.49.2:32423
root@Ubunty-22:/home/nazarvas#
```
![Image alt](https://github.com/nazarvas/Storage/blob/main/VirtualBoxVM_UrZQGCoY3i.png)

Удаляем наш сервис hello-minikube:
```sh
root@Ubunty-22:/home/nazarvas# kubectl delete services hello-minikube
service "hello-minikube" deleted
root@Ubunty-22:/home/nazarvas#
```

Удаляем развёртывание:
```sh
root@Ubunty-22:/home/nazarvas# kubectl delete deployment hello-minikube
deployment.apps "hello-minikube" deleted
root@Ubunty-22:/home/nazarvas#
```

# Практическое задание
1. Повторите практические примеры
2. Ознакомьтесь с minikube dashboard, выполнив данную команду в консоли;
3. Подробно рассмотрите команду minikube --help

1. Все примеры, которые были в лабе - есть в этом md.
2. 
```sh
root@Ubunty-22:/home/nazarvas# minikube dashboard
🔌  Enabling dashboard ...
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
💡  Some dashboard features require the metrics-server addon. To enable all features please run:
	minikube addons enable metrics-server	
🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
http://127.0.0.1:39175/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```
3. Подробно рассмотрел команду minikube --help
