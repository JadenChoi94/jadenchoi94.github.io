---
title: '[Django] SHELL이란'
excerpt: SHELL에 대해 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-11T09:00:00+09:00
toc: true
---


# Python Shell vs Django Shell

## Python Shell

파이썬은 코드가 한 줄 단위로 실행되는 인터프리터 언어입니다. 우리가 파이썬 파일에 엄청난 양의 코드를 적어도 결국 실행될 때는 'line by line' 즉 한줄 한줄씩 실행됩니다. 이때까지 우리는 여러 줄의 파이썬 코드를 파일 단위로 실행하는 것만 해봤습니다. 그런데 이것과 달리 파이썬은 코드를 한 줄씩 실행할 수 있는 환경도 존재하는데요. 파이썬이 제공하는 파이썬 쉘(Shell)이라는 프로그램을 실행하면 이런 환경을 만들 수 있습니다. 터미널 또는 명령 프롬프트에서 **'python'**을 입력하면 아래와 같이 파이썬 쉘이 실행됩니다.

![https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=3893&directory=Untitled.png&name=Untitled.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=3893&directory=Untitled.png&name=Untitled.png)

## Django Shell

Django도 위에서 설명한 쉘 환경, 즉 한 줄씩 Django 코드를 작성하고 실행할 수 있는 환경을 제공합니다. 우리가 앞에서 배웠듯 **manage.py**를 이용해서 **python manage.py shell** 커맨드로 실행할 수 있죠. 그리고 실행된 Django 쉘에서는 Django가 제공하는 모든 명령어를 자유롭게 사용해서 결과를 볼 수 있습니다.

![https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=3893&directory=Untitled%201.png&name=Untitled+1.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=3893&directory=Untitled%201.png&name=Untitled+1.png)

## Python Shell vs Django Shell

두 쉘의 모습만 보면 상당히 비슷해 보이죠? 사실 두 개 모두 같은 파이썬 쉘 환경입니다. 다만 Django 쉘은 Django 프로젝트를 쉘에서 바로 접근 할 수 있도록 하는 환경 설정이 더해진 것입니다. 

기존의 파이썬 쉘만으로는 Django에서 제공하는 기능을 사용할 수 없습니다. 이유는 Django가 사용할 환경 설정을 찾지 못하기 때문인데요, Django를 사용하기 위해서는 DJANGO_SETTINGS_MODULE이라는 환경 변수가 필요합니다. 이 환경 변수는 우리 프로젝트의 settings 파일의 위치를 Django에게 알려주는 역할을 합니다. 우리가 manage.py를 통해 Django shell을 실행하게 되면 이 환경 변수를 자동으로 시스템에 등록한 뒤 쉘을 실행하게 됩니다. 아래는 실제 manage.py 파일의 일부인데 보면 os.environ 즉 시스템의 환경 변수에 DJANGO_SETTINGS_MODULE을 등록하는 것을 볼 수 있습니다.

```
def main():
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'costaurant.settings')
    try:
        from django.core.management import execute_from_command_line
    except ImportError as exc:
        raise ImportError(
            "Couldn't import Django. Are you sure it's installed and "
            "available on your PYTHONPATH environment variable? Did you "
            "forget to activate a virtual environment?"
        ) from exc
    execute_from_command_line(sys.argv)
```

만약 Django의 manage.py를 통하지 않고 기존 파이썬 쉘에서 Django를 사용하고 싶다면 시스템의 환경 변수 목록에 DJANGO_SETTINGS_MODULE 환경 변수를 만들고 '<project_name>.settings'를 변수의 값으로 등록한 뒤에 아래 명령어를 사용해서 Django 쉘과 같은 환경을 만들 수 있습니다.

```
>> import django
>> django.setup()
```

이러한 설정을 굳이 할 필요는 없고 우리가 사용하는 Django 쉘이 어떤 것인지 정도만 알고 넘어가는 것으로 충분합니다. 정리하면 Django 쉘도 결국 파이썬 쉘인데 내가 만드는 프로젝트에 대한 설정이 들어가 있는 환경인 것이죠.