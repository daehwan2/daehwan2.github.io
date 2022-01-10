---
layout: single
title:  "프로퍼티 어트리뷰트"
categories: javascript
tag: [javascript, property=attribute, 프로퍼티어트리뷰트]
toc: true
---

**`내부 슬롯`** : 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 `의사 프로퍼티(pseudo property)`

**`내부 메서드`** : 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 `의사 메서드(pseudo method)`


<br/>

**`프로퍼티 어트리뷰트`** : 프로퍼티의 상태를 나타냄. 

*( 자바스크립트 엔진은 프로퍼티를 생성할때 프로퍼티 어트리뷰트를 기본값을 자동 정의한다 )*

프로퍼티 어트리뷰트에 직접 접근할 수 없지만 `Object.getOwnPropertyDescriptor` 메서드를 사용하여 간접적으로 확인할 수는 있다.

---

**`Object.getOwnPropertyDescriptor`메서드 :** 하나의 프로퍼티에 대한 프로퍼티 디스크립터 객체를 반환.

**`Object.getOwnPropertyDescriptors`메서드 :** 모든 프로퍼티의 프로퍼티 어트리뷰트정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다. (ES8에서 도입된 메서드)

---

```javascript
const person={
  name: "Lee",
};

person.age = 20;

console.log(Object.getOwnPropertyDescriptor(person,"name"));
//{value: 'Lee', writable: true, enumerable: true, configurable: true}

console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  age:{
  configurable: true     => 프로퍼티 재정의 기능 여부
  enumerable: true       => 프로퍼티 열거 가능 여부
  value: 20              => 프로퍼티 값(반환되는)
  writable: true         => 프로퍼티 값의 변경 가능 여부
  },
  name:{
  configurable: true
  enumerable: true
  value: "Lee"
  writable: true
  }
}
*/

```
<br/><br/><br/>

## 접근자 프로퍼티
자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.

**( getter/setter 함수라고 부르기도 함 )**

```javascript
const person = {
  //데이터 프로퍼티
  firstName: "Ungmo",
  lastName: "Lee",

  // 접근자 프로퍼티
  //getter
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  //setter
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },
};

console.log(person.firstName + " " + person.lastName);

//setter함수 호출
person.fullName = "Heegun Lee";
console.log(person);

//getter함수 호출
console.log(person.fullName);

let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log(descriptor);

descriptor = Object.getOwnPropertyDescriptor(person, "fullName");
console.log(descriptor);

```
<br/>

실행 결과
```
Ungmo Lee
{ firstName: 'Heegun', lastName: 'Lee', fullName: [Getter/Setter] }
Heegun Lee
{
  value: 'Heegun',
  writable: true,
  enumerable: true,
  configurable: true
}
{
  get: [Function: get fullName],
  set: [Function: set fullName],
  enumerable: true,
  configurable: true
}
```

<br/>
<br/>

# 프로퍼티 정의

```javascript
const person = {};

//데이터 프로퍼티 정의
Object.defineProperty(person, "firstName", {
  value: "Ungmo",
  writable: true,
  enumerable: true,
  configurable: true,
});

Object.defineProperty(person, "lastName", {
  value: "Lee",
});

let descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log("firstName", descriptor);

descriptor = Object.getOwnPropertyDescriptor(person, "lastName");
console.log("lastName", descriptor);

console.log(Object.keys(person));
// => lastName 프로퍼티는 [[Enumerable]]값이 false이므로 열거되지 않음.

person.lastName = "Kim";
//[[Writeable]]값이 false이므로 값을 변경할 수 없다.

delete person.lastName;
//[[Configurable]]값이 false이므로 삭제할 수 없다.

descriptor = Object.getOwnPropertyDescriptor(person, "lastName");
console.log("lastName", descriptor);

//접근자 프로퍼티 정의
Object.defineProperty(person, "fullName", {
  //getter
  get() {
    return `${this.firstName} ${this.lastName}`;
  },

  //setter
  set(name) {
    [this.firstName, this.lastName] = name.split(" ");
  },

  enumerable: true,
  configurable: true,
});

descriptor = Object.getOwnPropertyDescriptor(person, "fullName");
console.log("fullName", descriptor);

person.fullName = "Heegun Lee";
console.log(person.fullName);

console.log(person);

```

실행결과
```
firstName {
  value: 'Ungmo', 
  writable: true, 
  enumerable: true, 
  configurable: true
  }
lastName {
  value: 'Lee',
  writable: false, 
  enumerable: false, 
  configurable: false
  }
['firstName']
lastName {
  value: 'Lee', 
  writable: false, 
  enumerable: false, 
  configurable: false
  }
fullName {enumerable: true, configurable: true, get: ƒ, set: ƒ}
Kim Lee
{firstName: 'Kim', lastName: 'Lee'}

```

<br/>
<br/>
<br/>

### 한번에 정의하기
```javascript
const person = {};

Object.defineProperties(person, {
  //데이터 프로퍼티 정의
  firstName: {
    value: "Ungmo",
    writable: true,
    enumerable: true,
    configurable: true,
  },
  lastName: {
    value: "Lee",
    writable: true,
    enumerable: true,
    configurable: true,
  },

  //접근자 프로퍼티 정의
  fullName: {
    //getter 함수
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    //setter 함수
    set(name) {
      [this.firstName, this.lastName] = name.split(" ");
    },
    enumerable: true,
    configurable: true,
  },
});

person.fullName = "heegun Lee";
console.log(person); //{ firstName: 'heegun', lastName: 'Lee', fullName: [Getter/Setter] }

```
