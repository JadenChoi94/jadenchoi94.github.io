---
title: '[Django] Dockerize'
excerpt: 서버개발자 체험하기v3
categories:
    - Django

tag:
    - Django
    - python
    - DRF
    - docker
    - Nginx
    

comments: true
last_modified_at: 2021-06-22T19:00:00+09:00
toc: true
---

## 들어가기 전에

 지난 포스팅에서 nginx-uwsgi-django까지 연동을 했다. nginx와 django도 docker로 만들면 어떨까? 

최근 하나의 서버에서 모든기능이 동작하는 monolithic architecture에 상대대는 개념으로 각각의 기능별로 서버를 쪼개는 micro service architecture(MSA)가 살짝쿵 유행하고 있다. 물론 두 가지 모두 장단점이 있기 때문에 상황에 필요한 구조를 사용하는 것이 중요하다. 

docker가 등장하면서 컨테이너로 서버를 운영/배포/관리가 쉬워지고, 컨테이너를 관리하는 툴(오케스트라)이 등장하면서 MSA를 만드는데 용이해지고 있는데, 그래서 더더욱 MSA가 유행하는것 같다. 

하지만 docker로 서버를 구성하는것이 무조건 MSA가 되는 것은 아니고, 단순히 운영/배포적인 측면에서도 상당한 이점이 있기 때문에 dockernize(도커화)를 많이 하는 추세이다. 

그래서 이제 서버를 docker화 해보자~!

(docker에 대한 개념 설명은 **생략**한다. 구글링 하면 많이 나온다 ><) 

 

## Django -> docker로 만들기

일단 docker로 만들 건데 어디까지 docker로 만들지 범위를 정해야 한다. 범위를 정한다는 게 이상하게 들릴 수 있는데, 예를 들면 django만 docker로 만들고 nginx는 서버에 직접 설치하는 방식인지, 아니면 nginx와 django 모두 docker로 만들지를 정하는것이다. 

저희는 nginx-uwsgi-django 모두 docker로 만들어 보겠다.

 

우선 django를 docker로 띄워볼 건데 일단 docker를 설치해보자

```
curl -fsSL https://get.docker.com/ | sudo sh
sudo usermod -aG docker $USER # 현재 접속중인 사용자에게 권한주기
```

그리고 docker로 만들 서버의 루트 폴더를 만들어준다. 홈에서 mkdir로 적당한 이름으로 폴더를 생성한다. 

```
mkdir docker-server
```



지난 시간에 하던 django 프로젝트를 docker-server 폴더 안으로 이동시킨다. 

새로운 인스턴스에서 진행하고 있어서 git clone으로 새로 프로젝트를 따온다.

> 기존 인스턴스에서 그대로 진행해보았지만, docker run -p 8000:8000  server_dev/django 명령실행시, 0.0.0.0:8000 address is already in use 라는 에러가 발생하였다
>
> 2시간동안 위 에러를 해결하기위해 열심히 구글링해보며 여러시도를 해보았지만 번번히 실패하였다. 그래서 깔끔하게 새로운 인스턴스를 실행하여 새로 시작하였다. 
>
> 그래도 이런 삽질은 중요하다. 덕분에 ubuntu, django, docker의 많은 명령어들을 익혔다ㅎㅎ

```
ubuntu@ip-172-31-33-49:~$ cd docker-server/
ubuntu@ip-172-31-33-49:~/docker-server$ ll
total 8
drwxrwxr-x 2 ubuntu ubuntu 4096 Jun 13 16:38 ./
drwxr-xr-x 6 ubuntu ubuntu 4096 Jun 13 16:42 ../
ubuntu@ip-172-31-33-49:~/docker-server$ git clone https://github.com/tkdlek11112/server_dev.git
Cloning into 'server_dev'...
remote: Enumerating objects: 28, done.
remote: Counting objects: 100% (28/28), done.
remote: Compressing objects: 100% (18/18), done.
remote: Total 28 (delta 7), reused 24 (delta 6), pack-reused 0
Unpacking objects: 100% (28/28), done.
Checking connectivity... done.
ubuntu@ip-172-31-33-49:~/docker-server$ 
```

 요론 구조가 되었을 거다

└── docker-server
  └── server_dev 

 

이제 server_dev 안에 들어가서 Dockerfile을 만든다. **docker의 시작은 Dockerfile이다.**

vi Dockerfile로 파일을 생성하고 아래와 같이 적는다.

 

```
# /docker-server/server_dev/Dockerfile
FROM python:3.6.7  # 생성하는 docker의 python 버전

ENV PYTHONUNBUFFERED 1 

RUN apt-get -y update 
RUN apt-get -y install vim # docker 안에서 vi 설치 안해도됨

RUN mkdir /srv/docker-server # docker안에 srv/docker-server 폴더 생성
ADD . /srv/docker-server # 현재 디렉토리를 srv/docker-server 폴더에 복사

WORKDIR /srv/docker-server # 작업 디렉토리 설정

RUN pip install --upgrade pip # pip 업글
RUN pip install -r requirements.txt # 필수 패키지 설치

EXPOSE 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

 

이제 docker build 명령어를 통해 docker 이미지를 만들 수 있다. 

여기서 명령어중에 pip install -r requirements.txt가 있는데, 여기는 django에 필요한 패키지들이 나열되어 있다. 추가로 나중에 uwsgi를 사용하기 위한 uwsgi패키지도 명시되어있어야 하다. 이 포스팅에서 필요한 requirements.txt는 아래와 같다. 

```
# requirements.txt
asgiref==3.2.7
Django==3.0.5
pytz==2019.3
sqlparse==0.3.1
uwsgi
```

 

```
docker build -t server_dev/django .
```

 -t는 만드는 docker 이미지에 이름을 붙여주는 옵션이다. 그 뒤에 .은 docker 이미지로 만들 폴더 경로를 말하다. 이프로젝트는 ~/docker-server/server_dev/ 경로에 있기 때문에 .을 찍어서 현재 폴더를 지정하다. 



![img](https://blog.kakaocdn.net/dn/kVdSW/btqEPmXPbKy/pKzDHycFBY8ZSadFkDr5wk/img.png)step by step으로 진행됨

명령어를 실행하면 터미널 창에 이것저것 뜨면서 docker 이미지가 생성되는 것을 볼 수 있다. 

(명령어를 실행했는데 권한 에러가 뜬다면, 위에서 docker 권한 설정하는 명령어를 치고, ssh 재접속을 하면 될 거다) 

이미지가 정상적으로 만들어졌는지 확인하기 위해서는 docker image list라고 입력해보자

![img](https://blog.kakaocdn.net/dn/mltMM/btqEOP0HSZJ/KCHWTR4ODLmMfk61zgY5d1/img.png)생성된 이미지들을 확인할 수 있다.  

 -t 옵션으로 이름을 붙였던 server_dev/django가 있는 것을 확인할 수 있다. 그럼 이 이미지를 실행해보겠다. -p 옵션으로 서버 자체의 포트(호스트 포트번호)와 docker의 포트번호를 맞춰준다.

```
docker run -p 8000:8000  server_dev/django
```

 

![img](https://blog.kakaocdn.net/dn/Wqjbo/btqEOZPzE4u/6gWhg0wprvHXOYE5NzSflk/img.png)docker 이미지가 실행되면서 바로 django가 실행된다

Dockerfile에 runserver를 이용해 django를 실행하는 명령어를 적어놨기 때문에 docker 이미지를 실행하게 되면 runserver가 실행된다. 실제로 서버 url에 접속해보면 django 페이지가 뜨는 것을 확인할 수 있다. (방화벽 확인 필수~) 

 

## Nginx -> docker 만들기

이제 Nginx를 docker로 만들어보자. 사실 Nginx는 쉽다. Django야 이번 프로젝트를 docker에 넣기 위해 이것저것 해야 하지만 Nginx는 그냥 config 파일만 몇 개 있으면 된다. 아래 두 개의 config와 Dockerfile을 만들자. 

 

```
# ~/docker-server/nginx/nginx.conf
user root;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 1024;
    # multi_accept on;
}

http {

    ##
    # Basic Settings
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;
 # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL Settings
    ##

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # Logging Settings
    ##

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip Settings
 ##

    gzip on;
    gzip_disable "msie6";

    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ##
    # Virtual Host Configs
    ##

    # include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
# ~/docker-server/nginx/nginx-app.conf
upstream uwsgi {
    server unix:/srv/docker-server/apps.sock;
}

server {
    listen 80;
    server_name localhost;
    charset utf-8;
    client_max_body_size 128M;

    location / {
        uwsgi_pass      uwsgi;
        include         uwsgi_params;
    }

    location /media/ {
        alias /srv/docker-server/.media/;
    }

    location /static/ {
        alias /srv/docker-server/.static/;
    }
}

# ~/docker_server/nginx/Dockerfile
FROM nginx:latest

COPY nginx.conf /etc/nginx/nginx.conf
COPY nginx-app.conf /etc/nginx/sites-available/

RUN mkdir -p /etc/nginx/sites-enabled/\
    && ln -s /etc/nginx/sites-available/nginx-app.conf /etc/nginx/sites-enabled/

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

 

이제 django와 똑같이 build 한다. 

```
docker build -t server_dev/nginx .
```



![img](https://blog.kakaocdn.net/dn/wDbJv/btqEPfdEDPx/ZGy7F47ZsvdpKyJkv6DMD1/img.png) 

이제 nginx docker 이미지를 실행하다.

 

```
docker run -p 80:80 server_dev/nginx
```

 

![img](https://blog.kakaocdn.net/dn/nUdrS/btqEO0Ou3JU/C8qGsd01V6Yr3leU4MJVOK/img.png)된다?

실제로 url을 치고 들어가 보면 nginx서버가 가동 중인 것을 확인할 수 있다. 



![img](https://blog.kakaocdn.net/dn/b5ksIQ/btqENZXgX8Y/N0NHCWtkgSyzc22apd4ln0/img.png)하지만 bad gateway 

하지만 nginx 이후에 uwsgi.sock이 연결되지 않았기 때문에 django랑은 연결되지 않았다. 

이제 드디어 docker-compose를 사용할 때!!!  

 

## Nginx docker + django docker = docker-compose

 

docker 이미지를 두 개를 띄웠는데, 두 개의 다른 docker에서 서로 연결하려면 어떻게 할까? 사실 docker끼리 연결하기 위해 잡다구리한 옵션을 넣어서 docker run을 해야 하다. 하지만 그렇게 하면 불편하니까  docker-compose를 쓰자. 

docker-compose는 여러 개의 docker 이미지를 한 번에 관리하는 툴이다. 일단 설치! 

```
sudo curl -L https://github.com/docker/compose/releases/download/1.25.0-rc2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

 이제 docker-compose를 입력하면 무엇인가 실행되는 것을 확인할 수 있다.

docker-compose는 docker의 Dockerfile처럼 **docker-compose.yml**을 사용해서 설정값을 입력할 수 있다.

~/docker-server 경로에 docker-compose.yml 파일을 생성하다. vi docker-compose.yml 



![img](https://blog.kakaocdn.net/dn/rnuF8/btqEPldzk2Q/dzhOsIXL7RZmyzJBYHLkBk/img.png)요 위치

 

```
# docker-compose.yml
version: '3'
services:

    nginx:
        container_name: nginx
        build: ./nginx
        image: docker-server/nginx
        restart: always
        ports:
          - "80:80"
        volumes:
          - ./server_dev:/srv/docker-server
          - ./log:/var/log/nginx
        depends_on:
          - django

    django:
        container_name: django
        build: ./server_dev
        image: docker-server/django
        restart: always
        command: uwsgi --ini uwsgi.ini
        volumes:
          - ./server_dev:/srv/docker-server
          - ./log:/var/log/uwsgi
```

 

이렇게 한방 설정을 할 수 있다. 포트 설정을 docker-compose.yml에서 정의하고 uwsgi설정도 하기 때문에 uwsgi.ini를 만들고, 각각의 Dockerfile도 살짝 변경이 필요하다.

 

```
# ~/docker_server/nginx/Dockerfile
FROM nginx:latest

COPY nginx.conf /etc/nginx/nginx.conf
COPY nginx-app.conf /etc/nginx/sites-available/

RUN mkdir -p /etc/nginx/sites-enabled/\
    && ln -s /etc/nginx/sites-available/nginx-app.conf /etc/nginx/sites-enabled/

#EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

 

```
# /docker-server/server_dev/Dockerfile
FROM python:3.6.7  # 생성하는 docker의 python 버전

ENV PYTHONUNBUFFERED 1 

RUN apt-get -y update 
RUN apt-get -y install vim # docker 안에서 vi 설치 안해도됨

RUN mkdir /srv/docker-server # docker안에 srv/docker-server 폴더 생성
ADD . /srv/docker-server # 현재 디렉토리를 srv/docker-server 폴더에 복사

WORKDIR /srv/docker-server # 작업 디렉토리 설정

RUN pip install --upgrade pip # pip 업글
RUN pip install -r requirements.txt # 필수 패키지 설치

#EXPOSE 8000
#CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

 

```
# ~/docker-server/server_dev/uwsgi.ini
[uwsgi]
socket = /srv/docker-server/apps.sock
master = true

processes = 1
threads = 2

chdir = /srv/docker-server
module = server_dev.wsgi

logto = /var/log/uwsgi/uwsgi.log
log-reopen = true

vacuum = true
```

 

이제 모든 준비는 끝났다.

 

```
└── docker-server
    ├── docker-compose.yml
    ├── nginx
    │   ├── Dockerfile
    │   ├── nginx-app.conf
    │   └── nginx.conf
    └── server_dev
        ├── Dockerfile
        ├── manage.py
        ├── README.md
        ├── requirements.txt
        ├── server_dev
        │   ├── asgi.py
        │   ├── __init__.py
        │   ├── __pycache__
        │   ├── settings.py
        │   ├── urls.py
        │   └── wsgi.py
        └── uwsgi.ini
```

 위와 같은 구조로 폴더가 구성되어있다. 이제 ~/docker-server로 가서 docker-compose를 build 하고 실행한다. 

```
docker-compose up -d --build
```

 -d 옵션은 데몬 모드이다. docker-compose는 run이 아니라 up/down으로 관리한다.

실행하면 또다시 터미널에 명령어가 한가득 나온다. 그러고 나서 nginx와 django의 docker 이미지가 실행된다. 

실행이 완료되고 docker-compose ps를 입력하면 docker-compose 상태를 알 수 있다.



![img](https://blog.kakaocdn.net/dn/bxOenw/btqEO0VfXye/mWFK3s8K8zuPqT7kZKfvS0/img.png)docker-compose 상황

docker-compose라고 해서 docker와 별개인 것이 아닌 각각의 docker 이미지의 상황을 모아서 보여주는 도구일 뿐이다. 실제로 docker ps와 docker image ls를 입력하면 docker-compose.yml에 ./build로 정의했던 이미지들이 생성되고, 실행 중인 것을 확인할 수 있다. 



![img](https://blog.kakaocdn.net/dn/bH13PF/btqEOESHuQT/3rLBSAA4ay17Q77aCqPTEk/img.png)docker ps , docker image ls

그럼 nginx-uwsgi-django가 정상적으로 연결되었는지 확인해볼까?

url을 입력해 접속해 보자.  



![img](https://blog.kakaocdn.net/dn/4CciX/btqEPuuAbjZ/BEN03jQgtmOG36BUmuap21/img.png)



정상적으로 접속이 된다~!!

80번 포트로 접속했는데도 접속되는거 보니까. Nginx타고 django 까지 잘 연결된거 같다.

추가적으로  $docker-server/log/uwsgi.ini 파일을 열어보면 제일 하단에 GET방식으로 호출한것을 확인할 수 있다.

 ![get](C:\Users\User\Desktop\Workspace\GithubPage\assets\images\get.png)

 

## 정리하기

* docker를 이용해 nginx와 django 이미지를 생성하고, docker-compose를 이용해 두 개의 docker 이미지를 하나의 파일로 실행시켰다.  

* 만약 django 소스가 변경된다면 docker-compose down, docker-compose up으로 재실행하면 적용될거다.

   (Dockerfile이 변경되었다면 build부터 다시 해주어야 한다.) 

* docker-compose는 각각의 Dockerfile을 build 하고 run하는 역할로 터미널에서 docker run <option>으로 docker를 실행하는 것을 스크립트로 적어논것이라고 생각하면 된다. 
* 즉 docker-compose.yml만 있으면 되는것이 아니라 각각의 Dockerfile도 있어야 한다는 것. docker-compose는 각각의 docker를 build하고 상관관계를 정의해주어서 좀 더 쉽게 docker 이미지를 관리할 수 있게 해 준다.