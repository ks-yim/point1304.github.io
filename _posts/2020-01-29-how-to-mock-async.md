---
title: '비동기 호출 mock하기'
excerpt: 'how to mock async call in js'
categories:
  - Programming
tags:
  - javascript
  - 웹개발
---

javascript 개발시 `fetch` 와 같은 비동기로직을 간단히 클라이언트에서 테스트하고 싶은 경우가 있다.

`Promise`와 `setTimeout`을 이용하여 간단하게 구현 가능하다.

```javascript
const createMockApi = (resovleValue, delay) => {
  return () => new Promise(resolve => {
    setTimeout(
      () => resolve(resolveValue),
      delay
    )
  }
});
  
const getUser = createMockApi(
  {
    data: {
      user: {
        id: '1',
        username: 'someone',
        email: 'someone@example.com',
      }
    }
  },
  2000
);

getUser()
  .then(
    // run something...
  );
```
