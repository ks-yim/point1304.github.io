---
title: '비동기 호출 mock하기'
date: 2020-01-29T12:08:00+09:00
last_modified_at: 2020-01-30T11:44:00+09:00
excerpt: 'Promise와 setTimeout을 이용하여 비동기 호출 mocking'
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
  });
};
  
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
