---
title: '[Django] FormField'
excerpt: FormField함수에 대해 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-14T15:00:00+09:00
toc: true
---

# Django Form Field

Django 폼(Form)을 작성할 때 가장 중요한 부분이 바로 데이터에 맞는 폼 필드를 작성하는 것입니다. Django는 입력 데이터에 따라 사용할 수 있는 여러 내장 폼 필드를 제공하는데, 각각의 폼 필드는 그에 맞는 입력 위젯을 기본으로 가지고 있습니다. 아래는 Django에서 제공하는 몇 가지 필드 목록과 옵션들 입니다.

| 필드                | 설명                                                         | 옵션                                                         | 기본 위젯      |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- |
| CharField           | 문자열 입력을 위한 필드입니다.                               | max_length : 최대 길이 설정 min_length : 최소 길이 설정 strip : 문자열 앞뒤 공백을 제거합니다. (기본값: True) empty_value : 비어 있는 값을 나타낼 값 (기본값: 빈 문자열) | TextInput      |
| EmailField          | 이메일 입력을 위한 필드입니다.                               | CharField와 같은 옵션인자를 사용합니다.                      | EmailInput     |
| IntegerField        | 정수 입력을 위한 필드입니다.                                 | max_value : 최댓값 설정 min_value : 최솟값 설정              | NumberInput    |
| BooleanField        | True, False 입력을 위한 필드입니다. (기본적으로 입력을 위해 체크박스가 사용됩니다.) | 체크박스가 빈 값일 경우 False로 처리됩니다.                  | CheckboxInput  |
| ChoiceField         | 주어진 값 안에서 하나를 선택할 수 있는 형식의 필드입니다.    | choices : 선택 항목 들의 목록 인자로 각 선택 목록은 튜플 형식을 사용합니다. 예시: options = [('1', 'male'), ('2', 'female), ('3', 'other')] | Select         |
| MultipleChoiceField | 주어진 보기에서 여러개를 선택할 수 있는 형식의 필드입니다.   | ChoiceField와 같은 옵션인자를 사용합니다.                    | SelectMultiple |
| DateField           | 날짜 형식을 입력 받는 필드입니다.                            | input_formats : 날짜의 형식을 지정합니다. (https://docs.djangoproject.com/en/2.2/ref/settings/#std:setting-DATE_INPUT_FORMATS) | DateInput      |
| TimeField           | 시간 형식을 입력받는 필드입니다.                             | DateField와 같은 옵션인자를 사용합니다.                      | TimeInput      |
| DateTimeField       | 날짜/시간 형식을 입력 받는 필드입니다.                       | DateField와 같은 옵션인자를 사용합니다.                      | DateTimeInput  |

- 이 밖에도 데이터 형식에 맞는 몇 가지 내장 필드가 있습니다. 내가 사용할 데이터에 대한 필드가 있는지 궁금할 때는 항상 공식문서를 참고하세요. https://docs.djangoproject.com/en/2.2/ref/forms/fields/#built-in-field-classes
- widget과 관련된 더 많은 내용은 아래 공식문서를 확인하세요. https://docs.djangoproject.com/en/2.2/ref/forms/widgets/

아래는 필드를 정의할 때 사용할 수 있는 필드 옵션들 입니다.

| 인수         | 설명                                                         |
| ------------ | ------------------------------------------------------------ |
| required     | 필수적으로 입력해야 하는 항목 인지를 결정합니다. 기본값은 True이며 False일 경우 비워두는 것을 허용합니다. |
| label        | 해당 필드의 label 항목에 적힐 이름을 지정합니다. 만약 지정하지 않을 경우 폼 필드를 지정한 변수명의 첫 글자를 대문자로, 밑줄(_)이 있다면 띄어쓰기로 변경하여 label 값으로 사용합니다. |
| label_suffix | 기본적으로 label 다음 콜론(:)이 붙어서 표시되는데 이 값을 변경합니다. |
| initial      | 해당 필드에 초기값을 줄 때 사용합니다.                       |
| widget       | 해당 필드가 사용할 사용자 입력 UI, 즉 위젯을 지정합니다. 기본적으로 각 데이터 항목에 맞는 기본 위젯이 설정되어 있습니다. |
| help_text    | 입력에 도움이 되는 문자열을 입력 필드 밑에 표시합니다.       |
| validators   | 유효성 검증을 위한 검증 목록을 리스트 형태로 작성합니다.     |
| disabled     | 필드의 편집 가능 여부를 결정합니다. 기본 값은 False 이며 True일 경우 해당 필드가 보이지만 편집할 수 없습니다. |

- label

```python
# forms.py
from django import forms

class UserForm(forms.Form):
    name = forms.CharField()
    age = forms.IntegerField(label='Your age')
<!-- html -->
<label for="id_name">Name:</label> 
<input type="text" name="name" required id="id_name">
<label for="id_age">Your age:</label>
<input type="number" name="age" required id="id_age">
```

- label_suffix

```python
# forms.py
from django import forms

class UserForm(forms.Form):
    name = forms.CharField()
    age = forms.IntegerField(label_suffix='=')
<!-- html -->
<label for="id_name">Name:</label>
<input type="text" name="name" required id="id_name">
<label for="id_age">Age=</label>
<input type="number" name="age" required id="id_age">
```

- help_text

```python
# forms.py
from django import forms

class UserForm(forms.Form):
    name = forms.CharField(help_text='한글 이름을 작성해주세요.')
    age = forms.IntegerField()
<!-- html -->
<label for="id_name">Name:</label>
<input type="text" name="name" required id="id_name">
<span class="helptext">한글 이름을 작성해주세요.</span>
<label for="id_age">Age:</label>
<input type="number" name="age" required id="id_age">
```

![https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=4139&directory=Untitled.png&name=Untitled.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=4139&directory=Untitled.png&name=Untitled.png)


 더 많은 필드 옵션이 궁금하다면 아래 공식 문서를 참고하세요. https://docs.djangoproject.com/en/2.2/ref/forms/fields/#core-field-arguments