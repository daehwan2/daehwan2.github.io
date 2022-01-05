---
layout: single
title:  "단축 평가"
categories: javascript
tag: [javascript, shortened-evaluation, 옵셔널체이닝]
toc: true
---

# 단축 평가

 **`단축평가`는 표현식을 평가하는 도중에 평가 결과가 확정된 경우 나머지 평가 과정을 생략하는 것을 말한다.
  즉, 논리 연산의 결과를 결정하는 피연사자를 타입 변화하지 않고 그대로 반환다.**

  예를 보면 알기 쉽다.
  ```javascript
  //논리합(||) 연산자
  'Cat' || 'Dog'  // => 'Cat'
  false || 'Dog' // => 'Dog'
  'Cat' || false // =>'Cat'

  //논리곱(&&) 연산자
  'Cat' && 'Dog'  // => 'Dog'
  false && 'Dog' // => false
  'Cat' && false // => false 
  ```  

  문자열은 `true`이기 때문에 위와 같은 결과가 나오게 된다.
  좌항부터 순서대로 읽다가 이 연산의 결과를 결정하는 피연산자를 그대로 출력하는 것이다.

  정리해보면

  |   단축평가표현식   |    평가결과    |
  | :---: | :---: |
  | `true || anything` | `true` |
  | `false || anything` | `anything` |
  | `true && anything` | `anything` |
  | `false && anything` | `false` |

  <br/>
  <br/>

<br/>

<br/>


  # 단축평가가 유용한경우
  객체를 가리키기를 기대하는 변수가 `null` 또는 `undefined`가 아닌지 확인하고 프로퍼티를 참조할 때

  ```javascript
  var elem = null;
  var value = elem.value; // TypeError: Cannot read property 'value' of null
  ```

  원래 에러가나는 이 경우를 단축 평가를 이용하여 잡을 수 있다

  ```javascript
  var elem = null;
  var value = elem && elem.value; // => null
  ```


<br/>
<br/>

# 옵셔널 체이닝 연산자

ES11에서 도입된 옵셔널 체이닝 연산자 `?.`는 좌항의 피연산자가 `null`또는 `undefined`인 경우 `undefined`를 반환하고, 그렇지 않으면 우항의 프로퍼티를 참조한다.

```javascript
var elem = null;
var value = elem?.value;
console.log(value); // undefined
```

<br/>
<br/>

# null 병합 연산자

ES11에서 도입된 null병합 연산자 `??`는 좌항의 피연산자 `null` 또는 `undefined`인 경우 우항의 피연산자를 반환하고, 그렇지 않으면 좌항의 피연산자를 반환한다.

```javascript
var foo = null ?? 'default string';
console.log(foo); // "default string"
```

