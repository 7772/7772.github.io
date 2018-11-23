---
layout: post
title: React Native Jest 적용기
subtitle: 실무에서 처음 적용한 TDD 개발
---

TDD 는 나에게 무슨 장벽.. 같았다.

쓰임새는 알겠는데, 내 프로젝트에 적용하려니..

### 뭐부터 해야하지?

싶었다.

그러다 드디어 실무에 작게나마 적용해보게 되어, 글로 남기려한다.

---

### 해야할 일


1. 서버로 부터 배열이 들어온다.
```
const exampleArray = ["navigation", "black_box", ...];
```

2. 내 쓰임새에 맞게 배열을 바꾼다.

```
const myArray = [
    {
        iconPath: "....",
        optionName: "네비게이션"    
    },
    {
        iconPath: "....",
        optionName: "블랙박스"    
    }
];
```

3. 주어진 배열에 따라 Dynamic 하게 View 를 렌더링해준다.

```
- 만약 배열의 원소가 1개 이면?
0

- 만약 배열의 원소가 3개 이면?
0 0
0

- 만약 배열의 원소가 5개 이면?
0 0
0 0
0
```

---

### Test

1. TDD 를 적용하기 전에는

프로젝트를 띄우고, 방금 작성한 코드가 적용되는 View 단 까지 열심히 클릭, 클릭, 클릭 해가며 이동한다...
가끔은 오타가 난 사실을 그 View 단에 가서 에러코드를 보고 깨우친다...
수정하고, 다시 클릭, 클릭, 클릭 해가며 이동한다...
...반복..

이 과정을 수백차례 하다보면, 이 세상에 나만 이렇게 미련하게 개발하는게 분명하다는 생각이 들기 시작한다.

그래서 이제는

2. TDD 를 적용하자.

[Jest](https://jestjs.io/) 는 React, React Native 에 기본적으로 포함되어 있는 테스트 도구이다.

Jest 를 살펴보면 굉장히 다양한 기능이 포함되어 있는데,

...

난 아직 모른다..

그래서 일단, 내 코드가 정말 잘 써졌는가! 내 코드가 정말 원하는대로 View 를 렌더링 해주는가! 부터 좀 알아보자고 생각했다.

아니아니,,, 그전에 일단 서버로 부터 받은 Array 가 내가 원하는 모습으로 잘 바뀌는지 부터 테스트 해봐야하지 않나 싶었다.

그래서 이렇게 써보았다.

```
import HandleVehicleOptions from "../src/components/HandleVehicleOptions";

const options = [
  "navigation",
  "hiPass",
  "phoneHolder",
  "backCamera",
  "seatWarmer",
  "blackBox"
];

let vehicleOptions = HandleVehicleOptions.makeVehicleOptions(options);

test("transform correctly", () => {
  expect(vehicleOptions).toHaveLength(6);
});
```

배열이 맞게 변경 되었다면, 일단 6개 바꿨으니 6개 원소는 들어가있겠지 싶었다.

```
npm test vehicleOptions.test.js
```

통과.

딱, 여기까지 했을때, 이제 TDD 를 이해한 느낌이 들었다.

위의 고작 10 줄 남짓을 쓰는데 1년이나 주저하고 망설였다.

왜 이게 어려웠을까? 이전까지는 올바르게 배열이 바뀌었나를 보려면,

const vehicleOptions = HandleVehicleOptions.makeVehicleOptions(options); 
console.log(vehicleOptions); 

이렇게 확인했던 것이다.

ㅜㅜ...


내친김에, [react-test-renderer](https://github.com/facebook/react/tree/master/packages/react-test-renderer) 까지 한번 사용해보자고 마음먹었다.

문서를 읽어보니, react-test-renderer 는 컴포넌트의 snapshot 을 찍어줘서, Component 가 어떻게 변화하고 있는지 기록할 수 있게 해준다고 한다.

는데...무슨 말인지, 언제 써야하는지 역시나 모르겠었다.

일단 그려나 보기로 했다.

```
import renderer from "react-test-renderer";

import HandleVehicleOptions from "../src/components/HandleVehicleOptions";

const options = [
  "navigation",
  "hiPass",
  "phoneHolder",
  "backCamera",
  "seatWarmer",
  "blackBox"
];
let vehicleOptions = HandleVehicleOptions.makeVehicleOptions(options);

test("transform correctly", () => {
  expect(vehicleOptions).toHaveLength(6);

  const tree = renderer
    .create(HandleVehicleOptions.renderVehicleOptions(vehicleOptions))
    .toJSON();
  expect(tree).toMatchSnapshot();
});
```

이렇게 그려보면, __snapshots__ 라는 디렉토리가 하나 생성되고, 그 안에 vehicleOptions.test.js.snap 이라는 파일이 생성된다.

그 내용은, renderVehicleOptions 를 실행한 View 파일이 주루룩 들어있었다.

이후에, 만약 options 배열의 내용을 바꾸어 다시 테스트 해보면, 콘솔에서 어떤 것들이 달라졌는지 알려준다.

즉, 그 View 파일을 보면 일단 이건 확실해졌다.

"내가 만든 코드가 내 생각대로, 코드를 그리고 있다."

물론 UI 가 좀 틀어지거나, 화면 밖으로 삐죽 나온다던지 하는 문제가 있을 수는 있다.

그래도, 여기까지 TDD 를 적용해봄으로써

일단 기능 구현이 확실히 되었다는 것을 증명한 셈이다.

마음이 굉장히 꽈악 찬 느낌이 들었다.

그니까, 확신이 생긴거다. 

내 코드는 올바르다. 라는 확신!

TDD 가 눈앞에 있는 느낌이 든다.


