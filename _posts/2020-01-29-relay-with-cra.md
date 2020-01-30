---
title: Create React App(CRA)에서 Relay 사용하기
excerpt: Using Relay with CRA
date: 2020-01-29 16:13:00 +0900
categories:
  - Programming
tags:
  - React
  - Relay
---

Create React App(CRA)은 React 앱의 production 및 개발환경에서 필수적인
`code splitting`, `css autoprefixer`, `babel`, `hot-reloading` 등의 복잡한
`webpack`설정없이 npm/yarn을 통해 한방에 React 개발환경을 구축할 수 있도록 도와주는 툴이다.

다만, 세부적인 `webpack`설정을 수정할 수가 없어 `Relay`와 같이 추가적인 **babel** 혹은
**webpack** 설정을 필요로 하는 프레임워크와의 통합에 있어 유연성이 떨어진다.

## 1. CRA 환경에서 Relay사용을 위해 babel macro사용

공식 CRA 문서에서는 아래와 같이 babel macro를 통해 해결하는 방식을 제시하고 있다.

[\[CRA Adding Relay\]](https://create-react-app.dev/docs/adding-relay/#!)

```shell
npm install --save babel-plugin-relay
```

```javascript
// babel 플러그인 사용을 위해 macro를 import한다
import graphql from 'babel-plugin-relay/macro'

const query = graphql`
  query UserQuery {
    user {
      id
      username
    }
  }
`
```

위 방법은 잘 동작하고, import된 **babel-plugin-relay/macro**는 runtime이 아닌
compile time에만 동작하기에 외부 module을 import할때의 흔한 우려와는 달리
bundle 사이즈나 성능상의 손해는 없다. [\[참조\]](https://www.jackfranklin.co.uk/blog/saving-manual-work-with-babel-macro/)

심지어 **babel macro**의 사용을 옹호하는 측에서는 <U>(1)수작업 설정이 필요없다는 점</U>과
<U>(2)플러그인의 사용에 있어 외부 설정파일에 의존하지 않고 코드에 명시적으로 선언하는 점</U>을
장점으로 제시하며 `.babelrc`설정을 건드리는 것 보다 macro 사용을 권장하기도 한다.

## 2. react-app-rewired를 이용하여 CRA 환경에서 Relay사용

다만 위 방식은 `typescript`와 사용시 import한 module의 타입을 별도로 구현해줘야 하고,
compile시 사용되는 코드가 application코드에 자리하고 있는 부자연스러움 때문에
개인적으로 선호하는 방식은 아니다.

CRA를 eject하지 않고, **babel macro**를 사용하지 않으면서도 babel 설정을
customize하기 위해 우선 `react-app-rewired`와 `customize-cra`모듈을 설치한다.

```shell
npm install --save-dev react-app-rewired customize-cra babel-plugin-relay
```

그리고 프로젝트 root의 `package.json`의 "scripts"설정을 아래와 같이 변경한다.

#### **`package.json`**

```json
{
  // ...
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build"
  // ...
  }
}
```

이후 프로젝트 root에 `config-overrides.js`파일을 생성하고 아래와 같이
기존 CRA설정을 override하는 코드를 작성하여 CRA가 컴파일시 `.babelrc`파일을
참조할 수 있도록 한다.

#### **`config-overrides.js`**

```javascript
const { useBabelRc, override } = require('customize-cra');

module.exports = override(
  useBabelRc()
)
```

이제 마지막으로 `.babelrc` 파일을 프로젝트 root에 생성하여 relay 플러그인을
명시해주는 것으로 CRA에서 Relay를 사용할 수 있다.

#### **`.babelrc`**

```json
{
  "plugins": ["relay"]
}
```

#### **`SomeComponent.js`**

```javascript
// relay의 runtime 의존성만을 import 한다.
import { graphql } from 'react-relay'

// ...
const query = graphql`
  query UserQuery {
    user {
      id
      username
    }
  }
`
```
