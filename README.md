### netology-5.4
### Домашнее задание к занятию "5.4. Практические навыки работы с Docker"

#### Задание 1

В данном задании вы научитесь изменять существующие Dockerfile, адаптируя их под нужный инфраструктурный стек.

Измените базовый образ предложенного Dockerfile на Arch Linux c сохранением его функциональности.
```
FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -y software-properties-common && \
    add-apt-repository ppa:vincent-c/ponysay && \
    apt-get update
 
RUN apt-get install -y ponysay

ENTRYPOINT ["/usr/bin/ponysay"]
CMD ["Hey, netology”]
```
Для получения зачета, вам необходимо предоставить:

 - Написанный вами Dockerfile
 - Скриншот вывода командной строки после запуска контейнера из вашего базового образа
 - Ссылку на образ в вашем хранилище docker-hub


***Ответ:***
На `https://hub.docker.com/_/archlinux/` берем себе образ archlinux
```
docker pull archlinux

docker images
archlinux             latest    4f0cfa6f1ec5   7 days ago       382MB
```
Составляем `Dockerfile` файл для установки ponysay
```
FROM 4f0cfa6f1ec5

RUN yes | pacman -Suy  ponysay

ENTRYPOINT ["/usr/bin/ponysay"]
CMD ["Hey, netology"]
```
Собираем образ и запускам контейнер


```
docker build -t romrsch/ponysay -f Dockerfile .

docker images
romrsch/ponysay       latest    f31fcf56bcd2   2 minutes ago   515MB

docker run -ti romrsch/ponysay

```
Результат:

![alt text](https://i.ibb.co/x51chnF/ponysay.jpg)

```

user@ubuntu:~/netology/5.4/archlinux$ docker push  romrsch/ponysay
Using default tag: latest
The push refers to repository [docker.io/romrsch/ponysay]
afebb4a64106: Pushed 
82ad1577e768: Mounted from romrsch/netology5.3 
dfd488a286c9: Mounted from romrsch/netology5.3 
15176fdb9a61: Mounted from romrsch/netology5.3 
61172cb5065c: Mounted from romrsch/netology5.3 
9fbbeddcc4e4: Mounted from romrsch/netology5.3 
764055ebc9a7: Mounted from romrsch/netology5.3 
latest: digest: sha256:211805925ae524189432a7b59deed0f8328884e87736830b0b804ce5cd3cd24d size: 1780

```
Ссылка на хранилище docker-hub:

https://hub.docker.com/repository/docker/romrsch/ponysay

docker pull romrsch/ponysay:latest

---
#### Задание 2

В данной задаче вы составите несколько разных Dockerfile для проекта Jenkins, опубликуем образ в dockerhub.io и посмотрим логи этих контейнеров.

 - Составьте 2 Dockerfile:

Общие моменты:

Образ должен запускать Jenkins server
1. Спецификация первого образа:

   * Базовый образ - `amazoncorreto`
   * Присвоить образу тэг ver1

2. Спецификация второго образа:

   * Базовый образ - `ubuntu:latest`
   * Присвоить образу тэг `ver2`

3. Соберите 2 образа по полученным Dockerfile

4. Запустите и проверьте их работоспособность

5. Опубликуйте образы в своём dockerhub.io хранилище

Для получения зачета, вам необходимо предоставить:

- Наполнения 2х Dockerfile из задания
- Скриншоты логов запущенных вами контейнеров (из командной строки)
- Скриншоты веб-интерфейса Jenkins запущенных вами контейнеров (достаточно 1 скриншота на контейнер)
- Ссылки на образы в вашем хранилище docker-hub

***Ответ:***

1. Dockerfile: 
 - Базовый образ - amazoncorreto
- Присвоить образу тэг ver1

```
FROM amazoncorretto

RUN yum install wget -y
RUN wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
RUN rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

RUN yum update -y
RUN yum install jenkins -y
RUN yum install java-1.8.0-openjdk-devel -y

CMD ["/bin/bash"]

```
```
docker build -t romrsch5.4/ver1 -f Dockerfile1 .

docker run -p 8081:8080 -p 50001:50000 -w /usr/lib/jenkins/ -i -t romrsch5.4/ver1 java -jar jenkins.war
```
Результаты по `amazoncorreto`:

![alt text](https://i.ibb.co/NV2Q4sN/Dokerfile1-log.jpg)

![alt text](https://i.ibb.co/sJcSQgd/Jenkins1.jpg)

![alt text](https://i.ibb.co/ZHcbznT/Jenkins1-login.jpg)


2. Dockerfile: 
 - Базовый образ - ubuntu:latest
- Присвоить образу тэг ver2

```
FROM ubuntu:latest

RUN apt-get update
RUN apt-get install wget -y
RUN apt-get install gnupg -y

RUN wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | apt-key add -

RUN echo "deb https://pkg.jenkins.io/debian binary/" >> /etc/apt/sources.list

RUN apt-get update
RUN apt-get install jenkins -y
RUN apt-get install openjdk-11-jdk -y

RUN cd /usr/share/jenkins/

CMD ["/bin/bash"]
```
```
docker build -t romrsch5.4/ver2 -f Dockerfile2 .

docker run -p 8082:8080 -p 50002:50000 -w /usr/share/jenkins/ -i -t romrsch5.4/ver2 java -jar jenkins.war
```
Результаты по `ubuntu:latest`:

![alt text](https://i.ibb.co/sPpk3RR/Dokerfile2-log.jpg)

![alt text](https://i.ibb.co/r4Jb2xF/Jenkins2.jpg)

![alt text](https://i.ibb.co/R9fJNqx/Jenkins2-login.jpg)

docker ps:
![alt text](https://i.ibb.co/M2k0zPT/docker-ps.jpg)

Ссылки на образы в ваше docker-hub:

(Поменял имена для образов и добавил теги по заданию)
```
docker image tag db17d5686d6a romrsch/ver1:ver1
docker image tag cefd74173110 romrsch/ver2:ver2

REPOSITORY            TAG       IMAGE ID       CREATED             SIZE
romrsch/ver2          ver2      cefd74173110   6 minutes ago       138MB
romrsch/ver1          ver1      db17d5686d6a   7 minutes ago       138MB

docker push romrsch/ver1:ver1
docker push romrsch/ver2:ver2
```
Ссылки:
```
https://hub.docker.com/repository/docker/romrsch/ver1
https://hub.docker.com/repository/docker/romrsch/ver2
```

```
docker pull romrsch/ver1:ver1
docker pull romrsch/ver2:ver2

```
---
#### Задача 3

В данном задании вы научитесь:

- объединять контейнеры в единую сеть
- исполнять команды "изнутри" контейнера

Для выполнения задания вам нужно:

1. Написать Dockerfile:

    - Использовать образ `https://hub.docker.com/_/node` как базовый
    - Установить необходимые зависимые библиотеки для запуска npm приложения `https://github.com/simplicitesoftware/nodejs-demo`
    - Выставить у приложения (и контейнера) порт 3000 для прослушки входящих запросов
    - Соберите образ и запустите контейнер в фоновом режиме с публикацией порта
    - Запустить второй контейнер из образа `ubuntu:latest`

2. Создайть `docker network` и добавьте в нее оба запущенных контейнера

3. Используя `docker exec` запустить командную строку контейнера ubuntu в интерактивном режиме

4. Используя утилиту `curl` вызвать путь / контейнера с npm приложением

Для получения зачета, вам необходимо предоставить:

- Наполнение Dockerfile с npm приложением
- Скриншот вывода вызова команды списка docker сетей (docker network cli)
- Скриншот вызова утилиты curl с успешным ответом

***Ответ:***

Dockerfile
```
FROM node

RUN apt-get update
RUN git clone https://github.com/simplicitesoftware/nodejs-demo.git

WORKDIR /nodejs-demo/

RUN npm install -g nodemon
RUN npm install -g npm@7.5.1
RUN npm install
RUN sed -i "s/localhost/0.0.0.0/g" app.js

CMD npm start
```

```
docker build -t romrsch/node -f Dockerfile .

docker images 
REPOSITORY            TAG       IMAGE ID       CREATED              SIZE
romrsch/node          latest    075d5e62fcbd   About a minute ago   959MB

docker run -d -p 3000:3000 --net=bridge romrsch/node npm start
5dbdc6e806fd4861343e4dda40ab9c4178f7f4e4b8d2cbc6aaf0463ce72d292b

docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                       NAMES
5dbdc6e806fd   romrsch/node   "docker-entrypoint.s…"   34 seconds ago   Up 33 seconds   0.0.0.0:3000->3000/tcp, :::3000->3000/tcp   recursing_kapitsa

docker  exec -it recursing_kapitsa /bin/bash
root@5dbdc6e806fd:/nodejs-demo# 
root@5dbdc6e806fd:/nodejs-demo# apt-get update
root@5dbdc6e806fd:/nodejs-demo# apt-get install curl wget

```

```
ser@ubuntu:~/netology/5.4/2$ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "da88ccd15ee572c93ac4f9d5a79376b4a678bc4438a98c77995284ece2d4dd16",
        "Created": "2021-07-12T08:35:08.089232182+07:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "5dbdc6e806fd4861343e4dda40ab9c4178f7f4e4b8d2cbc6aaf0463ce72d292b": {
                "Name": "recursing_kapitsa",
                "EndpointID": "a9e138c992dacf363c82a1a035f5d8668d93dc15174a9469e6c92bf72f409994",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

```
Посмотреть список сетей
```
user@ubuntu:~/netology/5.4/2$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
da88ccd15ee5   bridge    bridge    local
b4a6fac30126   host      host      local
e5c5c3dcc35c   none      null      local
user@ubuntu:~/netology/5.4/2$
```

---




