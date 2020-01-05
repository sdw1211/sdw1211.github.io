---
published: false
title: ecmascript 2020 새로운 점
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

우선 이 특징을 알아보기 전에 정규 표현식을 활용해서 매칭되는 모든 것을 가져오는 가져오는 방법에 대해서 한번 알아보겠습니다.

## RegExp.prototype.exec() with /g

정규표현식에서 /g 옵션을 추가할 경우, exec() 명령을 여러번 호출해서 정규표현식과 매칭되는 결과 값을 계속 가져올 수 있습니다.

```javascript


```

# 참고자료

* https://2ality.com/2018/02/string-prototype-matchall.html
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/matchAll
* https://github.com/tc39/proposal-string-matchall


