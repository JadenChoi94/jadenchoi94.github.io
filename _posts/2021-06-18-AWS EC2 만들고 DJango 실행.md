---
title: '[Django] AWS EC2 만들고 Django 연결하기'
excerpt: 서버개발자 체험하기
categories:
    - Django

tag:
    - Django
    - python
    - DRF
    

comments: true
last_modified_at: 2021-06-18T10:00:00+09:00
toc: true
---

> 유투버 mychew_님을 보고 학습을 진행하였습니다!



## AWS EC2 만들고 연결해보기



![img](https://blog.kakaocdn.net/dn/dlARjV/btqC22tGsjZ/Aqtb2hA155e2801rbkkc70/img.png)ununtu 이미지 생성



AWS 가입하고 서비스에서 인스턴스 생성시작을 클릭한다. 이후 EC2 인스턴스 생성 단계 첫 번째에서 AMI(Amazon Machin Image)를 선택하게 되는데 여기서는 ubuntu 18.04를 선택한다. Amazon Linux 2도 많이 쓰기는 하는데 아무래도 ubuntu가 좀 더 레퍼런스가 많이 검색돼서 ubuntu로 결정했다고 한다. 회사에서는 redhat을 많이 쓰기도 하지만 개인 프로젝트할 때는 ubuntu가 최고라고 한다.

 

 

![img](https://blog.kakaocdn.net/dn/bfRzA2/btqCZ0jf6zy/99kOkBerjBOOKPM1jtEd60/img.png)다음, 1년 공짜로 쓸 수 있는 t2.micro사용

돈 내고 쓸건 아니기 때문에 프리티어로 사용 가능한 t2.micro를 선택한다. 그밖에 설정은 그냥 Default로 한다. 가장 중요한 건 마지막에 key-pair를 선택하게 되는데, 새로 생성을 누르고 다운받은 *.pem은 ssh접속을 할 때 키로 사용되기 때문에 꼭 잘 보관해야한다. 

 



![img](https://blog.kakaocdn.net/dn/bxosF5/btqCZ0KnnAr/ec6BV6k1j6FuLc1ufFtIK1/img.png)인스턴스 목록에 running중인 인스턴스가 있을거다.



![img](https://blog.kakaocdn.net/dn/bcxezm/btqCZ1CsJZK/HI2A4SKLqO564Inn7eRSq0/img.png)키 이름이 인스턴스 생성시 만들었던 key-pair와 같은지 확인한다.

![img](https://blog.kakaocdn.net/dn/lx9bn/btqCZ1CsV1H/JGjEJgqbOKNBmvbCky71Ik/img.png)putty에 aws 인스턴스의 ip주소를 적어준다.

 

putty를 사용해서 접속한다. ip주소랑 aws에서 다운받은 key를 세팅하면 바로 접속된다. 

 

putty로 키를 세팅할 때 한 가지 처리해줘야 하는 절차가 있는데, aws에서 키를 받으면 *.pem파일로 받는다. putty에서는 *.ppk파일이 필요하기 때문에 pem -> ppk 작업을 해줘야 한다. putty를 설치하면 자동으로 putty gen이라는 프로그램이 설치 되는데, 이 프로그램으로 키 형태를 바꿔줄 수 있다. 아래 url을 참고해보자.

https://www.puttygen.com/convert-pem-to-ppk

 

putty에서 ip주소와 키를 세팅하고, open을 누르면 명령 프롬프트 창이 하나 실행된다. 여기서 어떤 계정으로 로그인할지를 묻는데, ubuntu는 기본 루트 계정이 ubuntu이기 때문에 Login as를 ubuntu로 입력한다.

 



![img](https://blog.kakaocdn.net/dn/nmRsD/btqC3NvZUpn/U5YWDcKCy3aX11azx6bEv0/img.png)접속 완료



 

정상적으로 접속이 되면 위 그림처럼 못생긴 터미널 창이 실행된다. 

여기까지, AWS에서 이미지를 생성하고 접속까지 해보았다. 아무 짓도 하지 않았기 때문에 서버에는 아무것도 설치되어있지 않다. 이제 Django 프로젝트를 만들고 서버에 올려보도록 하자.  

 

 

## Django 프로젝트 만들고 서버에 올리기

일단 로컬에서 Django 프로젝트를 만든다. 서버에 올리는 건 git을 활용해 올릴 예정이기 때문에 로컬에서 프로젝트를 만들고 git에 등록한 다음 서버에서 pull 할 예정이다. 

 repo만든법이 기억안날때를 대비해서...

```
echo "# tobeserverdeveloper" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/jadenchoi94/tobeserverdeveloper.git
git push -u origin master
                
```

 

source tree 로 편하게 관리하는걸 추천한다. 



![img](https://blog.kakaocdn.net/dn/IzGKm/btqC48TZmwT/psc5cBKfkZ2HX1MKbXYYak/img.png)source tree, 요런식으로 관리해준다.



기본 프로젝트를 github에 올렸다. venv는 빼고 올렸기 때문에 나중에 서버에서 가상 환경을 만들어주고 띄워야 한다. 일단 로컬에서 이 Django 프로젝트가 정상 동작하는지 확인해보면...

 



![img](https://blog.kakaocdn.net/dn/bK0rYc/btqCZ04CAh4/Nqg6UTxK1KW5lx1OwWH6iK/img.png)정상동작확인



이제 이 소스를 고대로 서버에 올려서 띄워보자. 사실 올린다는 의미가 정확하지는 않다. 왜냐면 실제로는 로컬에서 github으로 올리고, 서버는 github에서 다운받기 때문. 실제로는 서버에서 github에 대고 pull 하는 느낌이다.  

다행히 ubuntu에 git은 설치되어 있지만 python3와 pip를 설치해야한다. python과 pip를 설치하고 나서 가상 환경을 만들기 위해 virtualenv를 설치한다.

 

```
sudo apt update
sudo apt install python3
sudo apt install python3-pip
sudo apt install virtualenv
pip install virtualenv
```

 

pip install virtualenv만 해서는 ubuntu에서 실행이 안되면 sudo apt install virtualenv도 해주자. 이제 가상 환경을 만들고 실행을 시켜보자.

 

```
virtualenv venv --python=python3
```

루트 경로에서 위와 같은 명령어를 입력하면 venv폴더가 생기고 venv가상 환경이 만들어진다. python3로 버전을 선택하는 것을 잊지 말자.

 



![img](https://blog.kakaocdn.net/dn/cQ1ZUJ/btqCZ1oXMma/eZc16O16leuplAFWAVLLy0/img.png)폴더에 이렇게 되었다면 완성!

 

이제 가상 머신을 실행시키기 위해 **source venv/bin/activate**를 입력한다. 그럼 라인 맨 앞에 (venv)가 생기면서 가상 환경 안으로 진입된다. Django를 실행시키기 위해 python manage.py runserver를 입력한다.



![img](https://blog.kakaocdn.net/dn/bjb6WN/btqC496qREJ/1ckBFVJxUnN629KP482IB0/img.png)그러면 이렇게 에러나는게 정상이다. 

사실  python3를 설치하고 pip도 설치하고 venv도 만들어줬지만, Django 패키지를 설치하진 않았다. 그 밖에도 여러 패키지가 필요할 텐데 이를 동기화시키려면 requirements.txt 파일이 필요하다. 로컬에서 pip freeze > requirements.txt를 입력한다. 



![img](https://blog.kakaocdn.net/dn/pmVHV/btqC2t5690L/GyZ6wQsikvuftICfpZlKe0/img.png)pip freeze는 설치된 패키지 목록을 보여주는 명령어다.

 

명령어를 실행하면 루트 폴더에 requirements.txt 파일이 생긴 것을 알 수 있다. 이걸 git에 등록해서 푸시하고, 서버에서 pull로 받는다.

 



![img](https://blog.kakaocdn.net/dn/Tk5K2/btqC5oI5eyz/2UqqRKxB8qYyQDKJatuLz1/img.png)source tree에서 새로생긴 파일을 commit하고 바뀐 내용을 즉시 푸시!!!

![img](https://blog.kakaocdn.net/dn/RZI4h/btqC21BwHmj/N9HTknKTF0zBLZr2cG15J0/img.png)서버에서는 pull하기! requirements.txt가 생겼다.

 

이제 requirements.txt에 명시된 패키지를 설치해보겠습니다. 간단하게 명령어 한 줄이면 된다. 

```
pip install -r requirements.txt
```

 

![img](https://blog.kakaocdn.net/dn/bq7qOJ/btqC5nKaPcZ/7AjEO7HGip8NmBfowiKcvK/img.png)자기가 알아서 버전에 맞게 설치한다.

 

이렇게 requirements.txt 파일을 활용하면 로컬에서 설치한 패키지들을 서버에서도 똑같이 설치할 수 있다. 로컬과 서버의 패키지를 동기화시켜주는데 아주 도움을 많이 주고 있는 녀석이다. 서버가 여러 대라면 더욱 그렇다. 

이제 makemigrations 해주고 다시 서버를 가동해본다.  



![img](https://blog.kakaocdn.net/dn/bcqRAs/btqCZZ5KvSy/yS9JB4Mbgc7DvxbykWDonK/img.png)정상가동!



정말 제대로 되었는지 서버에 접속해보자. 아까 putty로 접속했던 aws의 ip주소를 입력하고 뒤에 포트번호 :8000을 입력해본다. 

아마 입력하면 페이지를 찾을 수 없다고 나올거다

그 이유는, 첫 번째 Django내에서 보안이 걸려있고, 두 번째로 runserver실행 시 IP주소를 127.0.0.1로 하면 안 되고, 세 번째로 aws에서 방화벽 설정이 되어있기때문. 하나씩 풀어보자.

 

일단 첫 번째는 tobeserverdeveloper/settings.py에 있다. 

```
tobeserverdeveloper/settings.py

...

ALLOWED_HOSTS = ['*'] # '*'를 추가해서 모든 호스트를 허용한다.

...
```

 두 번째는 runserver 실행 시 뒤에 옵션으로 0.0.0.0:8000을 실행하면 된다.

세 번째는 AWS console에 들어가서 설정한다. 



![img](https://blog.kakaocdn.net/dn/rQOJx/btqC1GSeB2b/ywIPCkkIGvvVxqaOIwhyPK/img.png)인스턴스에 보안그룹을 클릭하면 보안그룹 설정 페이지로 이동.

![img](https://blog.kakaocdn.net/dn/bqnxrx/btqCZZEKCcY/auL6gSQDzXF2MdSQQf0fA1/img.png)인바운드 규칙 편집을 선택한다. 기본적으로 SSH만 등록되어 있을거다. 



![img](https://blog.kakaocdn.net/dn/k88iZ/btqC219pdjP/bu7mXwuxtwTEUfWlVcSk40/img.png)장고에서 사용할 8000번 포트를 등록한다. 위치무관을 등록하면 ::/0이 등록된다.



 

이제 모든 준비는 끝났다. 서버에서 python manage.py runserver 0.0.0.0:8000을 입력한다. 그전에 소스를 수정했으니 git에 push 하고 서버에서 pull 하는 것을 잊지 말자.

 



![img](https://blog.kakaocdn.net/dn/HsLvV/btqC0J2C7GR/nKIXwQFTOA9NxF1HKDPeYk/img.png)드디어 실행 완료~!



드디어 서버에서 Django 프로젝트가 돌아간다. 앞으로 로컬에서 개발하고 git에 push 하면, 서버에서 git pull을 통해 변경된 소스를 반영할 수 있게 되었다.

 

이제 남은 작업은 docker로 mysql 띄워서 연동하기와 nginx세팅이다.  

 

## 정리하기

 

이번 포스팅에서는 **AWS에서 EC2 인스턴스**를 생성하였고, 로컬 환경에서 **Django 프로젝트를 만들어 Github에 연동**했습니다. 그리고 **AWS EC2에서 git을 활용해 우리가 만든 Django 프로젝트를 불러와 실행**해 보았습니다. 

이제 어느 정도 환경을 잡아놨기 때문에 소스를 수정하고 서버에 반영하기 쉬워졌다.

 

\1. 로컬에서 소스 수정

\2. commit 후 push

\3. 서버에서 git pull로 변경된 소스 반영