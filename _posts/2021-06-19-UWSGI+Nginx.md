---
title: '[Django] Django+UWSGI+Nginx'
excerpt: 서버개발자 체험하기v2
categories:
    - Django

tag:
    - Django
    - python
    - DRF
    

comments: true
last_modified_at: 2021-06-19T10:00:00+09:00
toc: true
---

> 유투버 mychew_님을 보고 학습을 진행하였습니다!

## 들어가기 전에

 

지난 포스팅에서 AWS EC2 우분투 인스턴스를 만들고, Django를 설치하고 git과 연동하여 로컬에서 개발한 내용을 aws에 적용하는 과정을 진행했다. 하지만 Django를 단순히 runserver를 이용해서 띄웠다. runserver는 개발용으로 Django 프레임워크를 실행하는 것이기 때문에 실제 서비스에서는 사용하지 않는 명령어이다. 

mychew님은 Django로 웹서버 개발에 처음 입문할 때 가장 힘들었던 점이 바로 "웹서버"라고 했다. 처음에 단순히 runserver를 이용해 실제 서비스까지 한다고 생각했다. 하지만 그 앞단에 진정한 웹서버를 사용해야 했고, 웹서버와 Django 사이에 인터페이스도 있어야 했다. 이번 포스팅에서는 웹서버와 Django 사이에 인터페이스를 세팅해보자

 

 

## 전체적인 구조 정리

 

uWSGI를 설정하기 전에 우리가 지금 무엇을 하고있는지 먼저 정리하고 시작하는 게 좋을 것 같다. 위에서 언급한 것처럼 클라이언트에서 Django사이에 수많은 단계들이 있다. 그림으로 정리하면 아래와 같다.

 



![img](https://blog.kakaocdn.net/dn/bmvfcJ/btqDPY59Hti/sAEfRV4b2rFbkpAqpViWyK/img.png)출처 : https://uwsgi-docs.readthedocs.io/en/latest/



 

웹 클라이언트는 우리가 만든 Django 서버로 API를 호출하는 앱이나 웹이라고 보면 된다. 클라이언트 바로 옆에는 웹서버(Web Server)가 있다. Nginx, apache가 바로 웹서버이다.

 

**웹서버**의 역할은 클라이언트로부터 요청(request)을 받아 뒤에 웹 어플리케이션으로 전달하고, 그에 대한 응답(respone)을 다시 클라이언트로 전달하는 역할이다. 단순히 **전달자(sender) 역할만** 한다고 생각하시면 된다. 받는 요청 전부 어플리케이션 쪽으로 전달하는 것은 아니고 단순히 보여주는 데이터(정적인 데이터라고 표현한다. HTML/CSS나 이미지, 파일 등을 가리킵니다)는 애플리케이션으로 전달하지 않고 바로 응답을 준다. 

 

웹서버 옆에 있는 것은 **WSGI**이다. 웹서버와 WSGI사이에 **소켓(Socket)**이 있는데 웹서버와 WSGI사이에 데이터를 주고받기 위한 인터페이스로 사용된다. 소켓은 네트워크를 사용한 HTTP 소켓이 될 수도 있고, 파일을 사용한 소켓(Linux 전용)이 될 수도 있다. 

 

**WSGI**는 풀어쓰면 **Web Server Gateway Interface**이다. WSGI는 웹서버와 웹 프레임워크 사이에 통신을 담당하는 인터페이스이다. 즉 웹서버와 Django 사이에 통신을 담당한다. WSGI의 종류는 여러 가지가 있는데 여기서는 uWSGI를 사용한다. 즉 **WSGI**는 개념이고 그것을 구현한 것 중 하나가 **uWSGI**이다.

 그 뒤는 우리가 이미 구현한 **Django**와 **Python Function**이 있다.  

 

## uWSGI 세팅

이제 putty를 이용해 AWS에 접속한다. uWSGI는 pip를 이용해 설치할 수 있다. 가상 머신을 켜는 것을 잊지 말자~! source venv/bin/activate 



![img](https://blog.kakaocdn.net/dn/bfX4WU/btqDRLxyYoQ/4dX7TqSLkt1HdQK22UVqFK/img.png)pip install uwsgi



```
pip install uwsgi
```

 

설치하고 나면 현재 프로젝트에 기본적인 세팅이 되어있다. 실행만 시키면 된다. uwsgi를 입력하면 아래와 같은 화면이 나온다. 



![img](https://blog.kakaocdn.net/dn/ISGEW/btqDRNa44a8/A7A81yo00ZEsxGGf0soG8K/img.png)기본적인 설정값들이 나온다. 대신 옵션을 넣지 않았기 때문에 실행되지는 않는다.



 

기본적으로 설정돼있는 값을 가지고 uwsgi가 실행되는 것을 알 수 있다. 현재 Django 프로젝트를 uwsgi에 연결시키려면 다음과 같은 명령어를 입력하면 된다.

 

```
uwsgi --http :8000 --module server_dev.wsgi
```

 

여기서 server_dev는 프로젝트 이름이다. 만약 프로젝트가 다른 이름이라면 server_dev대신 다른 이름으로 넣으면 된다. --http :8000 옵션을 넣으면 ip주소:8000으로 접속 가능하다. 

![img](https://blog.kakaocdn.net/dn/DqZZz/btqDQsZ2Di8/4fBay2DPKcX5uCeZgnJuG0/img.png)이전과 다르게 worker가 하나 구동되었다고 뜬다.



이제 uwsgi를 통해 서버가 구동되었다. 테스트는 간단한다. runserver로 했을 때와 같이 서버에 접속해보면 된다. 유튜버님은 duckdns라는 사이트를 통해 AWS 주소에 대한 도메인을 등록했기 때문에 choaws.duckdns.org:8000로 들어갈 수 있었다. 도메인등록을 안했으면 AWS EC2의 IP주소를 입력해서 들어가면 된다.

 



![img](https://blog.kakaocdn.net/dn/IupET/btqDRLEmaBW/YcVFivO1WuCi78zHbkrdh0/img.png)django가 동작한다.



 페이지를 들어갔다 나왔다 하면 콘솔에 로그가 찍히는 것을 확인할 수 있다. 폰트나 아이콘을 찾을 수 없다고 나오는데 그냥 무시하시면 된다.

 

이제 default 설정이 아니라 커스텀 설정 파일을 만들어보자. 구글링 해보니 설정 파일 경로의 대세는 **/etc/uwsgi/sites/인가 보다.** 대세를 따르기 위해 /etc/안에 uwsgi와 sites폴더를 만들어준다. 폴더 만드는 건 mkdir이다.

 

폴더를 만들고 해당 경로로 이동한 다음 vi를 이용해 설정파일을 만듭니다.

```
#vi server_dev.ini
[uwsgi]
base = /home/ubuntu/server_dev

home = %(base)/venv
chdir = %(base)
module = server_dev.wsgi:application

socket = /tmp/django.sock
chmod-socket = 666

master = true
enable-threads = true
pidfile = /tmp/django.pid

vacuum = true
logger = file:/tmp/uwsgi.log
```

 

**home**은 가상 머신 위치, **chdir**은 프로젝트 경로, **module**은 프로젝트이름.wsgi:application을 적으면 된다. ini파일 안에서는 home경로를 ~로 표기할 수 없으니 절대 경로로 적어야 한다. socket은 나중에 linux소켓을 이용한 통신을 할 때 사용할 소켓의 경로를 나타낸다. 구글링 해보니 로그파일이나 소켓 파일은 대부분 tmp경로에 만든다.

> **꼭 여기서 module은 프로젝트이름과 동일 한지 확인하자 이거때문에 3시간 동안 삽질했다.**

**master**옵션은 master프로세스를 띄우냐 마냐인데 uwsgi로 하나의 앱을 관리할 경우는 띄우는 게 좋다. master프로세스가 데몬 역할을 해줘서 worker 프로세스들이 죽으면 알아서 띄워준다. 추가적으로 master프로세스만 찍을 수 있는 로그도 있다. 

 

**enable-threads**옵션은 멀티 스레드를 사용할지 말지 결정하는 옵션이다. 스레드 수를 설정해주지 않으면 기본적으로 1개로 뜨는데 쓰레드가 1개일 때도 쓰레드 사용 옵션을 킬 수 있다. 만약 threads옵션을 2개 이상으로 설정하면 enable-threads옵션을 켜지 않아도 자동으로 켜지게 된다. 



**pidfile**은 uwsgi프로세스들의 pid(process id)가 적혀있다. 리눅스에 익숙한 분들은 아시겠지만 ps -ef를 해서 프로세스 목록을 쫙 뛰우면 각각의 프로세스마다 pid를 가지고 있다. 강제로 프로세스를 종료할 때 kill pid를 주곤 하는데 pid를 맨날 ps -ef | grep uwsgi로 찾기 번거로우니 따로 파일로 만들어주는 옵션이다. cat /tmp/django.pid 해보면 프로세스 번호가 출력되는 것을 알 수 있다. 



**vacuum옵션은** uwsgi가 종료되고 나면 tmp파일을 지울지 말지 옵션이다. 설정해두면 uwsgi가 내려갔을때 .sock파일이나 .pid파일들이 지워진다.  

 

이제 config파일을 다 만들었으니 config파일을 이용해서 uwsgi를 실행하자. 아래와 같이 입력하면 된다.

```
uwsgi --http :8000 -i /etc/uwsgi/sites/server_dev.ini
```

 

![img](https://blog.kakaocdn.net/dn/xFkfJ/btqDQUB3rj1/RPjkFxqyvyUpftRHCTBmRK/img.png)옵션파일을 읽어왔다는 메시지만 뜹니다.



예전에는 이것저것 설정값들이 뜨고 로그도 찍혔는데 아마 ini로 실행하면 설정 파일을 잘 읽었다는 말만 나오고 멈춰있다. config에서 log파일 경로를 지정해주었기 때문에 앞으로 로그는 tmp/uwsgi.log로 남을 거다. 실제로 /tmp/경로로 이동해보면 아래와 같이 몇 개의 파일이 생긴 것을 확인할 수 있다.

 



![img](https://blog.kakaocdn.net/dn/bCpTB0/btqDQcb72AJ/ZDvkHk207j89IBKqQQwzkK/img.png).sock .log .pid 파일이 생성된것을 확인할 수 있다.



로그를 보고 싶으면 tail -f uwsgi.log를 하시면 실시간으로 확인할 수 있다. sock파일이 생기긴 했지만 uwsgi를 실행할 때 http 옵션을 주었기 때문에 여기서는 사용되지 않습니다. 나중에 웹서버랑 연계할 때 사용된다.  

길고 긴 uwsgi 세팅이 끝났다. 이제 웹서버로 넘어가보자.

 

 

 

## Nginx 세팅

 

Nginx는 pip로 설치하지 않고 apt-get으로 설치한다. 

```
sudo apt-get install nginx
```

 설치할꺼니? y/n이 나오면 y 누르고 엔터~! 



![img](https://blog.kakaocdn.net/dn/beP7PB/btqDTke0U2Y/GuD84lihDbjPrv20opyJN1/img.png)nginx -v 로 설치되었는지 확인 가능

apt-get으로 nginx를 설치했다면 /etc/nginx/ 폴더가 생겼을거다. 여기에 config들이 모여있는데 디폴트 설정이 되어있기 때문에 살짝 변경해준다. 

가장 상위 config는 /etc/nginx/nginx.conf이다. vi로 열어보자~  



![img](https://blog.kakaocdn.net/dn/3bSls/btqDVvnywEz/FcrjosJzmBPPstg5ZEdTc0/img.png)아래로 쭈우우욱.. 겁나 많이 있음..

 

아마 겁나 많은 config가 쓰여있다. uwsgi도 그렇고 nginx도 그렇고 config가 상당히 많은데, 쓰여있는 것만 간단히 집고 넘어가자.

 

------

 

nginx config는 {}를 사용해 "블록"단위로 나누어진다. 블록밖에 있는 config도 있는데 이를 core 모듈이라고 한다.

 

### 1. core 모듈 

 

**user** : nginx 프로세스가 실행되는 계정을 말한다. 루트 계정으로 하지는 않고 보통 사용자 계정 중에 하나를 선택한다.

 

**worker_process** : nginx 프로세스가 실행되는 수이다. auto로 하면 서버에 cpu 수만큼 뜬다.

 

**pid** : uwsgi 설정에서도 했었는데 nginx 프로세스들의 pid를 모아놓는 파일을 생성하는 설정이다.

 

**include** : 이것 어디서나 사용될 수 있는데, 특정 경로에 있는 .conf 파일을 읽어온다. 설정 파일들을 나누어 관리할 수 있게 해주는 역할이다.

 

### 2. event 블록

 

**worker_connections** : 하나의 worker 프로세스가 처리할 수 있는 커넥션 수이다. nginx는 비동기로 처리하기 때문에 하나의 프로세스가 다수의 커넥션을 가질 수 있다.

 

**multi_accept** : nginx가 동시에 커넥션을 받을지를 정하는 config이다. 위에 worker_connections 설명할 때 하나의 worker 프로세스가 여러 개 커넥션을 갖는다고 했는데 이건 뭐지?라고 생각할 수 있는데, multi_accept는 '동시에 커넥션을 받는가?'에 대한 설정이다. 기본값은 off인데, off일 경우 한 번에 하나의 커넥션을 생성해서 worker_connections에 설정된 수만큼 생성하는 것이고, multi_accept가 on일 경우 한 번에 50개 혹은 100개씩 커넥션을 생성할 수 있다. 이렇게 되면 한번에 들어오는 connection이 worker_connections보다 많으면 에러가 발생하게 되니 상황을 고려해서 on 해야 한다.

 

### 3. http 블록

 

**sendfile** : 응답을 보낼 때 파일을 read(), write()하게 되는데, 사용자 영역에서 파일을 읽고 쓰는 것이 아니라 커널 영역의 buffer를 사용해 속도를 향상하는 옵션이다.

 

**tcp_nopush** : sendfile이 on일 때만 사용 가능한 옵션으로, 리눅스에서 TCP_CORK옵션을 사용해 통신한다. TCP_CORK란 쉽게 설명하면 네트워크 통신에서 패킷을 여러 개를 보내지 않고 한번에 보내서 통신 속도를 향상하는 방법이다. 왜 여러 번 보낼때보다 한번이 좋냐면, 패킷을 보낼때 항상 헤더 패킷이 앞에 붙기 때문에, 여러번 보내면 전송되는 헤더 패킷이 많아지기 때문이다.

 

**tcp_nodelay** : tcp_nodelay는 tcp_nopush와 반대되는 개념이다. TCP_CORK를 사용하면 패킷을 여러 개 모아서 보낸다고 했는데, tcp_nodelay는 아무리 작은 패킷도 바로바로 전송하는 옵션이다. 구글링 해보면 1바이트를 보낼 때 헤더 붙고 뭐 붙고 하고, 다른 패킷 없나 찾아보다가 0.2초 정도 늦게 나간다고 한다. 근데 tcp_nodelay 키면 바로 나가는 거죠. 이 옵션은 keepalive_timeout 옵션이 켜있어야 한다.

 

**keepalive_timeout** : 응답을 보내고 얼마나 커넥션을 유지할지를 설정하는 옵션이다. 여러 번 통신을 하는 거면 커넥션을 맺는 일조차 낭비이기 때문에 이 옵션을 켜는 게 좋고, 단일로 발생한다면 끄는 게 좋다. 켜져 있으면 그만큼 커넥션이 살아있는 거라서 리소스를 잡아먹는다.

 

**type_hash_max_size** : 호스트 도메인 이름 공간이라고 한다. 넉넉하게 잡아야 긴 이름 할 수 있다.

 

**server_tokens** : 에러 페이지에 서버 버전 노출할지 옵션. 재밌는 옵션이네

 

**server_names_hash_bucket_size** : 서버 이름을 저장하고 있는 해쉬인데 도메인이 늘어나면 늘려줘야 한다고 하네요

 

**server_name_in_redirect** : on일 경우 redirect로 요청이 올 경우 server_name에 첫 번째 이름을 사용한다.

 

**include /etc/nginx/mime.types, default_type** : 이 두 개는 쌍으로 붙어있는데, mime.type을 열어보면 파일 확장자 별로 어떤 type의 데이터인지 정의되어있다. 정의되어있지 않는 type일 경우 default_type에 정의된 type으로 인식한다. application/octet-stream으로 되어있는데 바이너리 파일로 인식한다는 뜻이다.

 

**ssl_protocols** : ssl 프로토콜을 어떤 것을 사용할지 명시되어있다. 거의 건 드는 일이 없다고 한다.

 

**ssl_prefer_server_ciphers** : sslv3이나 TLS 프로토콜을 사용할 때 서버에 있는 암호화 방법을 사용할지 정하는 옵션이다. 

 

**access_log, error_log** : 로그파일의 경로를 지정하는 옵션이다.

 

그다음은 압축방법인 gzip에 대한 설명인데 자세한 설명은 외부에서 긁어왔다.

 

```
gzip [on/orr] : gzip의 설정을 on/off하는 명령어이다.
gzip_disable [regex] : 해당 명령어 다음에 오는 문장이 요청 헤더에 User-Agent와 일치하면 gzip을 하지 않습니다.
gzip_vary [on/off] : gzip, gzip_static, or gunzip의 설정들이 on으로 되어있을 때 응답 헤더에 “Vary: Accept-Encoding”를 넣을지 말지에 대한 명령어 이다.
gzip_proxied : 요청과 응답에 따라서 프록시된 요청에 대해서 gzipping을 할지 말지에 대한 설정이다. “any”로 설정하면 모든 응답에 대해서 gzipping을 수행한다.
gzip_comp_level [level] : 응답에 대한 압축의 정도를 의미하며 level의 값은 1부터 9까지 가능한다.
gzip_buffers [num] [size] : num은 버퍼의 갯수를 의미하며 size는 그 버퍼의 크기를 의미한다.
gzip_http_version [versino] : minimum HTTP 통신의 버전을 정한다.
gzip_types [types in array] : “text/html”을 제외하고 MIME 타입에서 또 어떠한 응답 형태들을 gzipping 할지 정할 수 있다.

출처:https://twpower.github.io/48-set-up-gzip-in-nginx
```

 

그 아래 제일 중요한 virtual host configs가 있는데 호스트들의 설정값들이 저장된 폴더를 include 한다. conf.d에는 아무것도 없을 것이고, sites-enabled폴더에는 default가 하나 있을 겁니다. 이 default를 지우고 uwsgi로 연결되도록 설정하면 된다.

 

### 4. mail 블록

 

디폴트로 주석 처리되어있는데 생략하겠다.

 

------

 

다시 nginx 설정으로 넘어와서 nginx의 virtual host config로 config파일을 하나 생성한다. 경로는 /etc/nginx/sites-enabled/이다.

 



![img](https://blog.kakaocdn.net/dn/IrzKP/btqDVvupGP5/L3eaYRhviBueKQfWBZ1M2k/img.png)디폴트는 무시하고 server_dev를 생성한다.



 



![img](https://blog.kakaocdn.net/dn/m8FE4/btqDUFYNez8/ZVEZJT5nxzHHjF5reCKPpk/img.png)설정은 간단하다~!



여기도 몇 가지 설정값들이 있다.

 

**upstream** : 서버 그룹을 정의하는 곳이다. server [주소 or 소켓]으로 서버 목록들을 나열할 수 있다. uwsgi는 소켓으로 띄웠던 거 기억하죠? /tmp/django.sock으로 띄웠었는데 그거 적어주면 된다.

 

**server** : 서버에 대한 상세 정의이다. listen으로 포트를 적고 server_name은 localhost를 적는다. client_max_body_size는 요청이 올라올 때 최대 크기를 정하는 건데 입맛대로 해주면 된다. location이 위치를 잡아주는 건데 uwsgi로 보내기 때문에 uwsgi_pass django, include는 uwsgi_params(자동으로 설치되어있음)를 하면 된다.  

 

이제 모든 준비가 끝났다. nginx를 가동하면 /etc/nginx/nginx.conf에서 /etc/nginx/sites-enables/server_dev를 읽어서 80(http) 포트번호로 들어오는 요청을 uwsgi로 보낼 거다. uwsgi는 장고로 보내기 때문에 장고 페이지가 뜨겠죠? nginx를 가동하기 전에 /etc/nginx/sites-enables/default를 조금 수정해야 한다. 왜냐면 여기도 80번 포트를 사용하게 되어있기때문.

 



![img](https://blog.kakaocdn.net/dn/c311HQ/btqDVemafw4/WV0go1K5aHcFoipTjDhry0/img.png)나는 그냥 8080으로 변경했다. 

이제 nginx를 실행하겠다. (uwsgi도 실행하자)  

nginx를 기동 하는 건 sudo systemctl start nginx를 입력하면 된다. 



![img](https://blog.kakaocdn.net/dn/bkGA6j/btqDVuChzjF/Jcj6WnPTn6q828yfAY6hn0/img.png)ps grap을 해보면 떠있다! 

이제 aws 주소로 들어가 볼까요?



![img](https://blog.kakaocdn.net/dn/OF7Jv/btqDTj2XEgn/ZpQcUNFFzkj0gPiMaoaOw0/img.png)짠~ 정상적으로 동작한다.



 

정상적으로 동작!! 이면 좋겠으나 아마 여러분은 페이지를 찾을 수 없다고 뜰 겁니다.

방화벽 설정을 해줘야 하기 때문이죠. aws 콘솔로 들어갑니다.

 



![img](https://blog.kakaocdn.net/dn/djDVl2/btqDSjbl6Cp/JB213hhZKGhCT3BtaQQPn0/img.png)HTTP를 추가해준다. 혹시 나중을 위해서 HTTPS도 추가해도 된다. :)

방화벽 설정까지 마치면 이제 접속이 될거다.! 

드디어 client(브라우저) -> nginx -> socket -> uwsgi -> django 가 완성되었다!!! 

 

## Mysql 세팅

mysql은 도커로 띄울 건데 따로 정리한 포스트가 있어 링크를 참고하면 된다. 어렵지 않다.

 

[2020/02/23 - [Study/서버\] - docker로 mysql, redis 실행. Django와 연결](https://cholol.tistory.com/476)

[ docker로 mysql, redis 실행. Django와 연결서버 개발을 하다 보면, 앱 서버와 DB서버가 분리되어있는 경우가 많다. (사실 거의 대부분이다.) 또 로컬에서 개발환경을 만들때 앱 서버 따로 DB따로 띄워주는 것이 상~~당히 귀찮고 복잡하다. 다행히 최근에는..cholol.tistory.com](https://cholol.tistory.com/476)

 

ubuntu docker 설치 

```
curl -fsSL https://get.docker.com/ | sudo sh
sudo usermod -aG docker $USER # 현재 접속중인 사용자에게 권한주기
sudo usermod -aG docker your-user # your-user 사용자에게 권한주기
```

**권한을 주지 않으면 django와 연결이 안된다~!**

 

### 정리하기 

이번 포스팅에서는 아래 3가지를 진행했다. 

1. uwsgi 설치 및 세팅

2. nginx 설치 및 세팅

3. docker로 mysql - django연결

 

이제우리의 장고는 웹서버를 통해 실행되게 된다. 대단하죠? runserver에 비해서 성능이 월등히 향상되었을 거다. 다음 시간부터는 api를 만드는 일을 해볼 건데, 실제로 서버 개발자가 제일 많이 하는 일이다. 

이번 시간에 했던 설정값들은 아주 중요한 지식이니 꼭 복습하고, 언급된 설정 이외에 많은 설정들을 검색해보시고 내것으로 만들어야한다.

특히 tcp_nopush나 tcp_nodelay 같은 설정은 면접에서 나오거 같은 질문들 같다.