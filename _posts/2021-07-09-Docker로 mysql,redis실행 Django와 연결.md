---
title: 'Docker로 mysql, redis 실행. Django와 연결'
excerpt: redis에 대해 이해하기
categories:
    - Docker

tag:
    - Docker
    - redis
    - mysql
    

comments: true
last_modified_at: 2021-07-09T12:00:00+09:00
toc: true
---


## [docker로 mysql, redis 실행. Django와 연결]



서버 개발을 하다 보면, 앱 서버와 DB서버가 분리되어있는 경우가 많다. (사실 거의 대부분이다.)

또 로컬에서 개발환경을 만들때 앱 서버 따로 DB따로 띄워주는 것이 상~~당히 귀찮고 복잡하다.

다행히 최근에는 컨테이너 형태의 이미지로 서버를 띄우는 경우가 많아 생각보다 간단하게? 구성할 수 있다. 서버 자체가 컨테이너 이미지로 되어있다면 더더욱 쉽다. (그냥 서버에 있는 이미지를 로컬에서 띄우면 된다)

내가 일하는 회사의 경우 앱, DB 모두 서버에 설치하여 구동중이다. 앱은 EC2에 Django를 설치하여 서비스하고, DB는 AWS RDS서버를 사용 중이다. 보통 테스트할 때 베타 서버에 있는 DB에 붙어서 테스트를 했는데, 이번에 서버 플로우나 DB구조를 대대적으로 변경하기 위해 로컬에서 작업환경을 만들어봤다.

일단 계획은 Django는 로컬에서 그냥 실행하고 MySql과 Redis만 docker로 띄워 연결할 것이다. 이전에 PostgreSql은 도커로 설치해봤는데 MySql은 처음이다.

 

## MySQL 띄우기

 

명령어는 터미널에서 `docker pull mysql`를 입력하면 mysql을 다운받는다.

 

docker를 설치할 때 이런 명령어들을 다 기억하는 것이 아니라 docker-hub에 들어가면 쉽게 찾을 수 있다.

 



![img](https://blog.kakaocdn.net/dn/GGFHo/btqBziqSzQf/TIi4utvaha882TOnVeZza0/img.png)docker hub에서 mysql 검색

![img](https://blog.kakaocdn.net/dn/RqXJf/btqBzkvtYmQ/WxKubbo4tDkOVF9E2SMUvk/img.png)클릭하면 좌측에 명령어가 나온다



 

hub 페이지에 실행하는 명령어도 나오는데 나는 아래와 같이 실행했다.

```
docker run -it --rm --name mysql-db -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -e MYSQL_PASSWORD=password mysql
```

 

그럼 이제 마구마구 명령어가 뜰 것이다. 아마 실행이 되었을 텐데 실행되었는지 확인하려면 다음과 같은 명령어를 쳐보면 된다.

 



![img](https://blog.kakaocdn.net/dn/TTzrG/btqBuxv5Zvi/OkKRnZChmXut6M1XoHHjEk/img.png)docker ps 로 실행중인 이미지를 확인할 수 있다.



```
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': '127.0.0.1',
        'NAME': 'mysql-db',
        'USER': 'root',
        'PASSWORD': 'password',
        'PORT': '3306',
        'OPTIONS': {'charset': 'utf8mb4'},
    }
}
```

Django에서도 DB설정을 위와 같이 로컬로 바꿔주니 바로 접속 가능! 신나게 makemigrations를 때렸다.

 

## Redis 띄우기

 

MySql로 docker를 사용해 봤으니 Redis도 쉽다. 사실 먼저 이미지를 docker pull img로 받지 않아도, docker run을 실행하면 이미지가 있는지 검사해서 있으면 실행, 없으면 다운로드 후 실행된다. 

아직 redis img를 설치하지 않은 상태에서 아래 명령어를 실행하면 다운로드 후 실행된다.

 

```
docker run --rm --name some-redis -d -p 6379:6379 redis
```

 

이 명령어를 치면 또 뭐라고 쭈우우욱 나오는데 docker ps 명령어로 정상 실행되었는지 확인할 수 있다.

 

 



![img](https://blog.kakaocdn.net/dn/2Wo2n/btqCbUXSGbJ/TWw77W9EXJ3YuYdjZOxkK1/img.png)docker ps



Django에서 docker 설정 역시 settings.py에서 할 수 있다.

 

```
#settings.py

CACHES = {
	"default": {
    	"BACKEND" : "django_redis.cache.RedisCache",
        "LOCATION" : "redis://127.0.0.1",
        "OPTIONS": {
        	"CLIENT_CLASS" : "django_redis.client.DefaultClient",
        }
    }
}
```

 

위와 같이 선언하면 Django에서 redis를 연결할 수 있다. 연결만 한다고 사용되는 것은 당연히 아니고, 코드상에서 redis를 사용하도록 명시해야 한다. django.core.cache패키지를 사용해서 cache를 사용하면 django에 등록된 cache를 사용하는데, redis를 등록했기 때문에 redis를 사용하게 된다. 

 