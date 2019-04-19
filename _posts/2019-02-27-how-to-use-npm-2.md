---
layout: post
title: 자바스크립트에서 모듈을 관리하는 방법
comments: true
categories: Javascript
---

## 자바스크립트에서의 모듈


**1. 모듈이란?**

자동차는 크게 엔진, 바퀴, 핸들, 차체 등으로 구성되어 있다.

한 공장안에서 자동차에 들어갈 모든 품목을 만들 수도 있다.

그러나 한 예로, 바퀴는 자동차에도 쓰이고, 비행기에도 쓰일 수 있다.

따라서 바퀴를 만드는 공장을 따로 만들어서 그 공장을 필요할때마다 불러서 사용한다.

프로그래밍에서 모듈은 위의 개념과 동일하다. 

자주 사용되는 것(기능)들을 따로 빼서, 필요할때마다 불러서(호출하여) 사용하겠다는 개념이다.

**2. 모듈을 왜 사용하는가?**

- 코드 추상화 : 특수한 라이브러리에 기능을 위임하여 실제 구현의 복잡도를 이해할 필요가 없다.
- 코드 캡슐화 : 코드를 변경하지 않으려면 모듈 내부에 코드를 숨긴다.
- 코드 재사용 : 같은 코드를 반복해서 작성하는 것을 피한다.
- 의존성 관리 : 코드를 다시 작성하지 않고도 쉽게 의존성을 변경한다.

## 모듈을 사용하는 방법 (CommonJS, ES6) 

자바스크립트에서 모듈을 사용하는 방법은 실행환경에 따라 다양하다.

대표적으로 [AMD](https://github.com/amdjs/amdjs-api/wiki/AMD), [UMD](https://github.com/umdjs/umd), [CommonJS](https://nodejs.org/docs/latest/api/modules.html) 그리고 ES6 의 `import, export` 문법이다.

**1. CommonJS**

- 모듈 가져오기

  ```
  // 전체 모듈 가져오기
  var module = require('./someModule.js');

  module.someMethod();
  ```

- 모듈 내보내기

  ```
  // 모듈 객체 전체를 내보내기

  var moduleName = {};

  moduleName.sayHello = function() {
    console.log('hello');
  };

  moduleName.sayGoodbye = function() {
    console.log('goodbye');
  };

  module.exports = moduleName;
  ```

  ```
  // 하나의 속성만을 내보내기

  var sayHello = function() {
    console.log('hello');
  };

  // module.exports.sayHello = sayHello; 와 동일!
  exports.sayHello = sayHello;
  ```

**2. ES6**

- 모듈 가져오기

  ```
  import moduleName from './someModule.js';

  moduleName.someMethod();
  ```

- 모듈 내보내기

  ```
  const moduleName = {};

  moduleName.sayHello = function() {
    console.log('hello');
  };

  moduleName.sayGoodbye = function() {
    console.log('goodbye');
  };

  function foo() {
    return 'foo';
  };
  
  export { moduleName, foo };
  ```

## References

- [codepink github page](https://github.com/codepink/codepink.github.com/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%AA%A8%EB%93%88,-%EB%AA%A8%EB%93%88-%ED%8F%AC%EB%A7%B7,-%EB%AA%A8%EB%93%88-%EB%A1%9C%EB%8D%94%EC%99%80-%EB%AA%A8%EB%93%88-%EB%B2%88%EB%93%A4%EB%9F%AC%EC%97%90-%EB%8C%80%ED%95%9C-10%EB%B6%84-%EC%9E%85%EB%AC%B8%EC%84%9C)

- [MDN import](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/import)
- [MDN export](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/export)



