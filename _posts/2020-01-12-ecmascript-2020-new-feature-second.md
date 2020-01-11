---
published: true
title: ecmascript 2020 새로운 점 - dynamic import
date: 2020-01-12T00:00:00.000Z
categories:
  - blog
tags:
  - ecmascript
---
ecmascript는 2015년 이후 매년 새로운 기능을 추가해서 버전을 업그레이드 하고 있습니다. ecmascript 2020에서 새로운 기능에 대해서 알아보도록 하겠습니다. 두번 째로 소개할 내용은 dynamic import입니다.

# dynamic import란?

기존의 방식과 다르게 ecmascript 모듈들을 동적(dynamic)으로 불어올 수 있는 기능입니다. 기존 방식과 비교해서 차이점에 대해서 알아보겠습니다.

## es2015에서의 static import

es2015에서 새로 추가되었던 import 문은 ecmascript 모듈들을 정적(static)으로 불러왔습니다. 이 말은 정의된 모든 모듈은 런타임 모드 전인 링킹(linking) 프로세스에서 불어온다는 것입니다. 런타임 시에 모듈을 변경한다던가 특정한 경우에 특별한 모듈을 받아오는 것은 불가능하였습니다. 거의 대부분의 경우에는 위와 같이 정적으로 가져오는 것이 좋습니다. 정적으로 가져올 경우에는 아래와 같은 장점들이 있기 때문입니다.

* 번들링할 때 사용하지 않는 코드를 제거할 수 있습니다.
* 번들링을 최소화 할 수 있습니다.
* 모듈에 있는 함수 검색을 더 효율적으로 하거나 변수를 확인 등을 통해서 개발자 도구에서 좀 더 효율적으로 코드를 작성할 수 있게 해줍니다.

기존 방식은 아래와 같습니다.

```javascript

import * as someModule from './aaaa/bbbb';    //.js 는 생략이 가능

```

위와 같이 모듈을 불러올 때 2가지 규칙이 있는데,

1. 프로그램의 가장 위에 있어야 합니다.
2. 모듈 정의자(module speccifier)의 경우 항상 고정되어야 합니다.('./aaaa/bbbb'와 같이 문자 리터럴 이어야 합니다. 변수는 불가 입니다.)

## dynamic import는 언제 필요할까요?

몇몇 상황에 대해서 dynamic 으로 모듈을 가져오는 것이 좋습니다. 

1. 특정한 상황에서만 모듈을 불어와야 하는 경우
2. 해당 모듈이 너무 크거나 사용 빈도가 낮을 때 프로그램의 성능을 키우기 위해서
3. 모듈을 불러오다가 오류가 발생할 경우에도 프로그램이 계속 동작해야 할 경우

웹 개발의 경우는 이런 dynamic import 를 할 수 있는 여러가지의 방법(lazing loading)을 제공해줬었는데, 이번에 공식적으로 이런 기능을 제공해주게 되었습니다. 어떻게 이것을 사용하게 되는지 알아보겠습니다.

## dynamic import 어떻게 사용할까요?

기본적인 형태는 아래와 같습니다.

```javascript

const javacafeModule = './modules/javacafeModules';

import(javacafeModule)
.then(module => {
  module.goJavacafe();
}).catch(err => {
  console.log('모듈 로딩 실패!!');
});

```

위 예제와 같이 함수형태로 되어 있고, 결과 값으로 promise 객체를 반환하게 됩니다. dynamic import 함수에서 받는 파라미터의 경우 당연히 문자 리터럴 뿐만 아니라 변수도 받을 수 있습니다. 또 하나 알고 있어야 하는 부분은 비록 함수 처럼 생겼지만 여기서 사용하는 import는 operator 입니다.

아래 예제를 통해서 좀 더 사용법에 대해서 알아보겠습니다.

```javascript

//default exports를 불어올 경우
const javacafeModule = './modules/javacafeModules';

import(javacafeModule)
.then(module => {
  module.default;
}).catch(err => {
  console.log('모듈 로딩 실패!!');
});

//awiat를 통해서도 불어올 있습니다.(async 함수 내에서만)

async function main() {
  const javacafeModule = './modules/javacafeModules';

  const module = await import(javacafeModule);
  //destructuring를 활용가능
  const {method1, method2} = await import(javacafeModule);
}

// 여러 개의 모듈을 한번에 로딩 가능
Promise.all([
    import('./module1.js'),
    import('./module2.js'),
    import('./module3.js'),
])
.then(([module1, module2, module3]) => {
});

```

이상으로 dynamic import 에 대해서 알아봤습니다. 다음에는 다른 새로운 특징에 대해서 알아보도록 하겠습니다.

# 참고자료

* https://github.com/tc39/proposal-dynamic-import
* https://2ality.com/2017/01/import-operator.html
* 

