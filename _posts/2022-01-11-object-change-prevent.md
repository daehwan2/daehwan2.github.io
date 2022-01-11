---
layout: single
title:  "객체 변경 방지"
categories: javascript
tag: [javascript, 객체변경방지]
toc: true
---
`Object.isExtensible 메서드:` 확장이 가능한 객체인지 알아보는 메서드.

`Object.isSealed 메서드:` 밀봉된 객체인지 여부를 알아보는 메서드.

`Object.isFrozen 메서드:` 동결된 객체인지 여부를 알아보는 메서드.

| 구분 | 메서드 | 프로퍼티 추가 | 프로퍼티 삭제 | 프로퍼티 값 읽기 | 프로퍼티 값 쓰기 | 프로퍼티 어트리뷰트 재정의 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 객체 확장 금지 | Object.preventExtensions | X | O | O | O | O |
| 객체 밀봉 | Object.seal | X | X | O | O | X |
| 객체 동결 | Object.freeze | X | X | O | X | X |
<br/>
<br/>
<br/>
<br/>
<br/>

# 객체 확장 금지
프로퍼티 추가만 금지
```javascript
const person = { name: "Lee" };

console.log(Object.isExtensible(person)); //true

Object.preventExtensions(person);

console.log(Object.isExtensible(person)); //false

person.age = 20; //확장이 금지된 객체이므로 무시
//stric mode 에서는 에러.
console.log(person); // { name: 'Lee' }

delete person.name; //삭제는 가능

console.log(person); // {}

//정의에 의한 추가도 불가.
Object.defineProperty(person, "age", { value: 20 });
//TypeError: Cannot define property age, object is not extensible

```
<br/>
<br/>
<br/>


# 객체 밀봉
밀봉된 객체는 읽기와 쓰기만 가능.
```javascript
const person = { name: "Lee" };

console.log(Object.isSealed(person)); //false

Object.seal(person);

console.log(Object.isSealed(person)); //true

console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {
    value: 'Lee',
    writable: true,
    enumerable: true,
    configurable: false   //밀봉된 객체는 configurable이 false다.
  }
}
 */

//프로퍼티 추가가 금지된다.
person.age = 20; //무시, strict mode에서 에러
console.log(person); // { name:"Lee" }

//프로퍼티 삭제가 금지된다.
delete person.name; //무시 ,strict mode 에서는 에러.
console.log(person); // { name:"Lee" }

//프로퍼티 값 갱신은 가능하다.
person.name = "Kim";
console.log(person); //{ name: 'Kim' }

// 프로퍼티 어트리뷰트 재정의가 금지된다.
Object.defineProperty(person, "name", { configurable: true });
//TypeError: Cannot redefine property: name

```

<br/>
<br/>
<br/>

# 객체 동결
동결된 객체는 읽기만 가능하다.

```javascript
const person = { name: "Lee" };

console.log(Object.isFrozen(person)); //false

Object.freeze(person);

console.log(Object.isFrozen(person)); //true

console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {
    value: 'Lee',
    writable: false,      //writeable 이 fasle다.
    enumerable: true,
    configurable: false   //configurable 이 fasle다.
  }
}
*/

//프로퍼티 추가가 금지된다.
person.age = 20; //무시, strict mode 에서는 에러.
console.log(person); //{ name: 'Lee' }

//프로퍼티 삭제가 금지된다.
delete person.name; //무시, strict mode 에서는 에러.
console.log(person); //{ name: 'Lee' }

//프로퍼티 값 갱신이 금지된다.
person.name = "Kim"; //무시, strict mode 에서는 에러.
console.log(person); //{ name: 'Lee' }

//프로퍼티 어트리뷰트 재정의가 금지된다.
Object.defineProperty(person, "name", { configurable: true });
//TypeError: Cannot redefine property: name

```

<br/>
<br/>
<br/>

# 불변 객체
변경 방지 메서드들은 얕은 변경 방지로 직속 프로퍼티만 변경이 방지되고 중첩 객체까지는 영향을 주지는 못한다.

```javascript
const person = {
  name: "Lee",
  address: { city: "Seoul" },
};

//얕은 객체 동결
Object.freeze(person);

//직속 프로퍼티만 동결된다.
console.log(Object.isFrozen(person)); //true
//중첩 객체까지는 동결 안됨.
console.log(Object.isFrozen(person.address)); //false

person.address.city = "Busan";
console.log(person); //{ name: 'Lee', address: { city: 'Busan' } }

```
객체의 중첩 객체까지 동결하여 변경이 불가능한 읽기 전용의 불변 객체를 구현하려면 객체를 값으로 갖는 모든 프로퍼티에 대해 재귀적으로 Object.freeze 메서드를 호출해야 한다.

```javascript
function deepFreeze(target) {
  //객체가 아니거나 동결된 객체는 무시하고 객체이고 동결되지 않은 객체만 동결한다.
  if (target && typeof target === "object" && !Object.isFrozen(target)) {
    Object.freeze(target);

    Object.keys(target).forEach((key) => deepFreeze(target[key]));
  }
  return target;
}

const person = {
  name: "Lee",
  address: { city: "Seoul" },
};

//깊은 객체 동결
deepFreeze(person);

console.log(Object.isFrozen(person)); //true
//중첩객체까지 동결
console.log(Object.isFrozen(person.address)); //true

person.address.city = "Busan";
console.log(person); //{ name: 'Lee', address: { city: 'Seoul' } }

```