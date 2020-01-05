---
published: true
title: ecmascript 2020 새로운 점 - string.prototype.matchAll
date: 2020-01-05T00:00:00.000Z
categories:
  - blog
tags:
  - ecmascript
---
ecmascript는 2015년 이후 매년 새로운 기능을 추가해서 버전을 업그레이드 하고 있습니다. ecmascript 2020에서 새로운 기능에 대해서 한 개씩 알아보도록 하겠습니다. 가장 먼저 소개할 내용은 String.prototype.matchAll 메소드입니다.

# String.prototype.matchAll란?

이 메소드는 정규표현식에 매칭되는 모든 결과 값의 iterator를 반환할 뿐만 아니고, 각 결과에 따르는 캡쳐 그룹(capturing groups)도 같이 반환해줍니다. 캡쳐 그룹(capturing groups))은 아래 링크를 통해서 확인해보시기 바랍니다.

[캡쳐 그룹](https://javascript.info/regexp-groups "캡쳐 그룹")

* 문법w
```javascript
str.matchAll(regexp) //결과 값으로 iterator가 반환
```

다른 메소드들과 차이점은 결과 값이 iterator가 반환된다는 것입니다. 이 iterator는 재시작 할 수 없기 때문에 만약 처음부터 다시 시작하고 싶으면 다시 메소드를 호출하거나 새로운 iterator를 생성해야 합니다.

## 정규표현식과 매칭되는 모든 데이터 가져오기

matchAll 메소드에 대해서 알아보기 전에 우선 자바스크립트에서 정규표현식을 통해서 매칭되는 모든 값을 가져오는 방법에 대해서 살펴보겠습니다. 이 것을 통해서 matchAll 이 왜 나오게 되었고 어떤 기능을 가지고 있는지 살펴보도록 하겠습니다. 우선 정규표현식을 통해서 "" 안에 있는 Text 값을 가져오는 예제를 하나 만들어 보겠습니다. 

### String.prototype.match

우선 String.prototype.match를 사용해서 하는 방법에 대해서 알아보겠습니다. String.prototype.match에 대한 설명은 아래 링크를 참고하시기 바랍니다.

[String.prototype.match](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/match)

```javascript
const str = '"javacafe" 만세 만세 "만만세"';
const regex = /"(.+?)"/g;

console.log(str.match(regex)); //[""javacafe"", ""만만세""]
```

위에서 저희가 원하는 경우는 ""안에 있는 Text를 반환하는 것이 었는데, match를 사용하면 ""를 포함한 값을 가져오게 됩니다. 이 가져온 결과를 map, replace 등의 함수를 통해서 ""를 제거하고 새로운 배열을 생성하면 되지만 이것보다 좀 더 효율적인 방법을 생각해보겠습니다. 이것보다 좀 더 효율적인 방법은 캡처 그룹을 활용하는 방법입니다.(캡처 그룹에 대해서는 위에 링크를 걸어놨으니 한번 보시고 오시면 도움이 될 것 같습니다.) 하지만 정규표현식에 g 옵션을 주고 사용할 경우 match 메서드를 사용하면 매칭되는 text의 배열만 결과 값으로 나오게 되어 캡쳐 그룹을 활용할 수 없게 됩니다. 캡쳐 그룹을 활용할 수 있는 방법은 무엇이 있을까요? 우선 RegExp.prototype.exec 메소드를 사용하는 방법을 통해서 알아보겠습니다.

### RegExp.prototype.exec

해당 메소드에 대한 설명은 아래 링크를 참고하시기 바랍니다.

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

위와 같이 할 경우 우리가 원하는 결과를 받을 수 있습니다. 하지만 위 프로그램의 경우 문제가 발생할 수 있습니다. 정규표현식에 g 옵션을 뺄 경우 exec의 경우 항상 처음 매칭되는 값을 가져오기 때문에, 해당 로직은 무한루프에 빠져들게 됩니다. 그럼 항상 정규표현식을 작성할 때 g 옵션을 넣었는지 안넣었는지 확인을 해봐야 하는 것입니다. 프로그램을 작성하는 것은 사람인지라 실수를 할 수 있는데 이 실수는 정말 큰 문제를 만들 수 있습니다. 그럼 이것보다 안전한 방법은 무엇이 있을까요? String.prototype.replace를 활용하는 방법이 있습니다.

### String.prototype.replace

해당 메소드에 대한 설명은 아래 링크를 참고하시기 바랍니다.

[String.prototype.replace](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String/replace)

```javascript
const str = '"javacafe" 만세 만세 "만만세"';
const regex = /"(.+?)"/g;
const result = [];
let match;

str.replace(regex, function(match, p1) {
  result.push(p1);
});

console.log(result); //["javacafe", "만만세"]
```
replace의 경우 2번 째 파라미터에 함수를 넘길 경우 함수 파라미터로 캡쳐 그룹 데이터를 받을 수 있습니다. 이것을 활용해서 구현하는 방법입니다.
이럴 경우 g 옵션을 실수도 빼먹었다고 하더라도 무한루프에 빠지지는 않게됩니다. 하지만 replace를 활용하는 것도 먼가가 이상합니다. replace 함수의 역할은 원래 무엇인가를 바꾸는 것인데 지금 사용한 것은 정규표현식과 매칭되는 데이터를 가져오는 역할을 하고 있습니다. 이는 해당 함수가 잘못된 역할로 사용되었다고 볼 수 있고 이 함수를 보고 어떤 역할을 하는지 파악하기 어렵게 됩니다. 이런 문제들(g 옵션 여부에 따른 문제, 직관성 문제등)을 개선하기 위해서 만든 함수가 String.prototype.matchAll 입니다.

### String.prototype.matchAll

동일한 기능을 하는 로직을 아래와 같이 수정할 수 있습니다.

```javascript
const str = '"javacafe" 만세 만세 "만만세"';
const regex = /"(.+?)"/g;

const result = Array.from(str.matchAll(regex), match => match[1]);
console.log(result);
```

String.prototype.matchAll를 활용하면 위와 같은 작성할 수 있습니다. matchAll의 캡처 그룹의 데이터를 포함하고 있기 때문에, 캡처 그룹의 데이터를 활용해서 우리가 원하는 결과를 받을 수 있습니다.

## iterator vs 배열

위 예제를 보시면 알겠지만, 위에 있는 다른 메소드들과 다르게 matchAll 의 결과 값은 배열이 아니고 iterator 입니다. iterator를 활용함으로써 보다 큰 캡처 그룹일 가지고 있거나 큰 사이즈의 문자열에 좀 효율적으로 사용할 수 있게 되었고, 만약 배열의 데이터가 필요한 경우에는 Arrray.from 또는 스프레드 연산자([...str.matchAll(regex)])를 활용해서 만들어서 사용하시면 됩니다.

## 정규표현식의 lastIndex

matchAll의 경우는 정규표현식을 내부적으로 복사해서 사용합니다. 그래서 string 스캔 후에도 기존 정규표현식의 lastIndex가 변경되지 않습니다. 정규표현식의 lastIndex에 대한 설명은 아래 링크를 참고해주시기 바랍니다.

[regexp.lastIndex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex)

```javascript
const regexp = RegExp('[a-c]','g');
regexp.lastIndex = 1;
const str = 'abc';

str.match(regexp);
console.log(regexp.lastIndex);  //0 이미 다 검색을 했기 때문에 lastIndex가 0으로 변경된다.

const regexp = RegExp('[a-c]','g');
regexp.lastIndex = 1;
const str = 'abc';

Array.from(str.matchAll(regexp), m => `${regexp.lastIndex} ${m[0]}`);
// Array [ "1 b", "1 c" ]
// 기존 정규표현식에는 영향을 주지 않는다.
```

이상으로 String.prototype.matchAll에 대해서 알아봤습니다. 다음에는 다른 새로운 특징에 대해서 한번 더 알아보겠습니다.

# 참고자료

* https://2ality.com/2018/02/string-prototype-matchall.html
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/matchAll
* https://github.com/tc39/proposal-string-matchall


