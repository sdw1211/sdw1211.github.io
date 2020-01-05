---
published: false
title: ecmascript 2020 새로운 점 - string.prototype.matchAll
date: 2020-01-05T00:00:00.000Z
categories:
  - blog
tags:
  - ecmascript
---
ecmascript는 2015년 이후 매년 새로운 기능을 추가해서 버전을 업그레이드 하고 있습니다. 이번 블로그에서는 ecmascript 2020에서 새로운 기능에 대해서 한번 알아보도록 하겠습니다.

# String.prototype.matchAll

이 메소드는 정규표현식에 매칭되는 모든 결과 값의 iterator를 반환할 뿐만 아니고, 각 결과에 따르는 캡쳐 그룹(capturing groups)도 같이 반환해줍니다. 갭쳐 그룹(capturing groups))은 아래 링크를 통해서 확인해보시기 바랍니다.

[캡쳐 그룹](https://javascript.info/regexp-groups "캡쳐 그룹")

## 정규표현식과 매칭하는 모든 데이터 가져오기

### String.prototype.match

한 예를 들어서 해당 메소드를 왜 사용하는지에 대해서 알아보겠습니다. 아래와 같은 text, 정규표현식이 있고 이것과 매칭하는 모든 결과 값을 가져올려고 합니다. String.prototype.match를 사용하면 해당 결과를 가져올 수 있습니다.

[String.prototype.match](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/match)

```javascript
const str = '"javacafe" 만세 만세 "만만세"';
const regex = /"(.+?)"/g;

console.log(str.match(regex)); //[""javacafe"", ""만만세""]
```

위 예제의 경우 ""안에 Text가 들어가 있는 모든 데이터를 가져오기 위한 것입니다. 그럴 경우 String.prototype.match 를 사용하면 위와 같은 결과를 가져올 수 있습니다. 그런데 여기서 ""안에 있는 Text만 가져오고 싶다면 어떻게 해야 할까요? 결과부터 말하면 이런 경우 캡쳐 그룹을 활용하면 쉽게 구할 수 있습니다. 하지만 정규표현식에서 g 옵션을 사용할 경우 String.prototype.match 를 통해서 나온 결과는 위와 같이 string 배열 값만 가져옵니다. 그럼 어떻게 하면 될까요?

### RegExp.prototype.exec

이럴 경우는 RegExp.prototype.exec 메소드를 사용합니다. 해당 메소드에 대한 설명은 아래 링크를 참고하시기 바랍니다.

[RegExp.prototype.exec](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)

```javascript
const str = '"javacafe" 만세 만세 "만만세"';
const regex = /"(.+?)"/g;
const result = [];
let match;

while(match = regex.exec(str)) {
  result.push(match[1]);
}

console.log(result); //["javacafe", "만만세"]
```

하지만 위의 경우도 문제가 있습니다. 정규표현식에서 g 옵션을 사용하지 않을 경우 항상 매칭되는 첫 번째 데이터만 가져오기 때문에 위의 경우는 무한 루프에 빠지게 됩니다. 이런 무한 루프를 피하기 위해서 아래와 같은 방법을 사용할 수 있습니다.

### String.prototype.replace

String.prototype.replace를 활용하면 위와 같은 기능을 구현 할 수 있습니다. 관련 설명은 아래 링크를 참고하시기 바랍니다.

[String.prototype.replace](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/replace)

```javascript
const str = '"javacafe" 만세 만세 "만만세"';
const regex = /"(.+?)"/g;
const result = [];
let match;

str.replace(regex, function(match, p1) {
  result.push(p1);
})

console.log(result); //["javacafe", "만만세"]
```
replace의 경우 2번 째 파라미터에 함수를 넘길 경우 함수 파라미터로 캡쳐 그룹 데이터를 받을 수 있습니다. 이것을 활용해서 구현하는 방법입니다.
이럴 경우 g 옵션을 실수도 빼먹었다고 하더라도 무한루프에 빠지지는 않습니다. 하지만 이런 식으로 프로그램을 작성하면 직관적이지 않습니다. replace라는 함수가 해주는 역할에 대해서 저희가 예상하는게 있는데 이 예상을 벗어나는 역할을 이 프로그램에서 하고 있기 때문입니다. 

### String.prototype.matchAll

위와 같은 문제점들 때문에 이것을 개선하기 위해서 나온 것이 String.prototype.matchAll 입니다.

```javascript
const str = '"javacafe" 만세 만세 "만만세"';
const regex = /"(.+?)"/g;

const result = Array.from(str.matchAll(regex), match => match[1]);
console.log(result);
```

String.prototype.matchAll를 활용하면 위와 같은 작성할 수 있습니다. 위에서도 설명했지만 matchAll 메소드를 활용하면 캡처 그룹에 대한 데이터를 받을 수 있기 때문에 위와 같이 활용할 수 있습니다. 여기서 중요한 점이 있는데 matchAll을 통해서 나온 결과 값은 iterator 라는 것입니다. 기존에 존재하는 match 등의 메소드에 대한 결과 값은 배열이었지만 matchAll 같은 경우는 iterator 입니다. 이 부분에 대해서 의문을 가질 수 있겠지만, 거대한 갭쳐 그룹이나 거대한 문자열을 사용할 경우 배열의 경우는 성능상에 문제가 발생할 수 있습니다.(배열의 경우는 연산을 다해서 결과를 반영해야 하지만 iterator의 경우는 next()를 호출할 때 마다 데이터 연산을 수행하기 때문에 성능 상 더 뛰어납니다.). 이런 점에 봤을 때 iterator 가 더 이점이 많기 때문에 신규 메소드에 대해서는 이런 식으로 처리하는 것으로 보입니다.

이상으로 String.prototype.matchAll에 대해서 알아봤습니다. 다음에는 다른 새로운 특징에 대해서 한번 더 알아보겠습니다.

# 참고자료

* https://2ality.com/2018/02/string-prototype-matchall.html
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/matchAll
* https://github.com/tc39/proposal-string-matchall


