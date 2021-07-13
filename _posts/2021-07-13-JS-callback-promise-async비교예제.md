---
title: '[JavaScript]callback, promise, async 예제'
excerpt: JS 공부하기
categories:
    - JavaScript

tag:
    - JavaScript
    

comments: true
last_modified_at: 2021-07-13T15:00:00+09:00
toc: true
---

![post-thumbnail](https://media.vlpt.us/images/suasue/post/0f53d2ec-91f6-4c33-983a-006f3f0b67c5/%EC%A0%9C%EB%AA%A9%EC%9D%84%20%EC%9E%85%EB%A0%A5%ED%95%B4%EC%A3%BC%EC%84%B8%EC%9A%94._001%20(16).png)

### flow

1. 사용자에게 id, password 입력 받아옴.
2. 로그인 시도, 로그인 성공하면 로그인한 사용자의 id를 받아옴
3. 그 id로 역할을 요청해서 받아옴.
4. 받아와서 사용자의 object를 출력

### callback

```js
class UserStorage {
  loginUser(id, password, onSuccess, onError) {
    setTimeout(() => {
      if (
        (id === 'ellie' && password === 'dream') ||
        (id === 'coder' && password === 'academy')
      ) {
        onSuccess(id);
      } else {
        onError(new Error('not found'));
      }
    }, 2000);
  }

  getRoles(user, onSuccess, onError) {
    setTimeout(() => {
      if (user === 'ellie') {
        onSuccess({ name: 'ellie', role: 'admin' });
      } else {
        onError(new Error('no access'));
      }
    }, 1000);
  }
}

const userStorage = new UserStorage();
const id = prompt('enter your id');
const password = prompt('enter your password');
userStorage.loginUser(
  id, 
  password,
  user => {
    userStorage.getRoles(
      user, 
      userWithRole => {
        alert(
          `Hello ${userWithRole.name}, you have a ${userWithRole.role} role`
        );
      },
      error => {
        console.log(error);
      }
    );  
  },
  error => {
    console.log(error);
  }  
);
```

콜백 지옥..

### promise

```js
class UserStorage {
  loginUser(id, password) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (
          (id === 'ellie' && password === 'dream') ||
          (id === 'coder' && password === 'academy')
        ) {
          resolve(id);
        } else {
          reject(new Error('not found'));
        }
      }, 2000); 
    });
  }

  getRoles(user) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (user === 'ellie') {
          resolve({ name: 'ellie', role: 'admin' });
        } else {
          reject(new Error('no access'));
        }
      }, 1000);
    });
  }
}

const userStorage = new UserStorage();
const id = prompt('enter your id');
const password = prompt('enter your password');
userStorage
  .loginUser(id, password)
  .then(userStorage.getRoles)
  .then(user => alert(`Hello ${user.name}, your have a ${user.role} role`))
  .catch(console.log);
```

### async, await

```js
class UserStorage {
  loginUser(id, password) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (
          (id === 'ellie' && password === 'dream') ||
          (id === 'coder' && password === 'academy')
        ) {
          resolve(id);
        } else {
          reject(new Error('not found'));
        }
      }, 2000); 
    });
  }

  getRoles(user) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (user === 'ellie') {
          resolve({ name: 'ellie', role: 'admin' });
        } else {
          reject(new Error('no access'));
        }
      }, 1000);
    });
  }

  async getUserWithRole(id, password) {
    try {
      const user = await this.loginUser(id, password);
      const userRole = await this.getRoles(user);
      alert (`Hello ${userRole.name}, your have a ${userRole.role} role`);
    } catch (error) {
      console.log(error);
    }
  }
}

const userStorage = new UserStorage();
const id = prompt('enter your id');
const password = prompt('enter your password');
userStorage.getUserWithRole(id, password);
```

> 참고사이트
> https://www.youtube.com/watch?v=aoQSOZfz3vQ