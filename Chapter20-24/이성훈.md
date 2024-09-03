# 20장 strict mode

## 20.1 strict mode란?

```jsx
function foo(){
	x = 10; // lexical scope => 블록이 정의된 위치를 기준으로 스코프 체인을 탐색한다.
					// 함수 스코프 내에 x 변수가 없다 -> 상위 스코프 체인인 전역 스코프 탐색
					// x라는 변수가 없으므로, 전역 객체의 x 프로퍼티를 동적 생성하고 10을 할당한다.
					// 암묵적 전역이라 한다.
}
foo();

console.log(x);
```

이러한 암묵적 전역 현상은 개발자의 의도와는 거리가 멀어 프로그램이 의도한 대로 동작하지 않는다. 따라서 반드시 ~~var~~, let, const 키워드를 사용하여 변수를 선언한 다음 사용해야 한다.
호이스팅 후 선언과 초기화가 동시에 진행되어 개발자의 선언문 이전에도 사용할 수 있는 var 키워드는 암묵적 전역 현상처럼 프로그램이 의도한 대로 동작하지 않을 확률이 높으며, 최악의 경우 이를 인지하지 못할 수도 있다. 따라서 선언과 초기화가 분리된 let과 const 키워드를 사용하여 이러한 잠재적 오류를 차단하는 것을 권장한다. let, const 키워드는 개발자의 선언문 이전 코드 영역에 해당하는 TDZ(일시적 사각 지대)에서는 초기화되어 있지 않다. 따라서 해당 변수를 참조할 시 
`ReferenceError: Cannot access 'a' before initialization`에러를 발생시켜 프로그램이 의도한 대로 동작하지 않을 시 개발자에게 알려주는 기능을 한다. 이처럼 안정적인 코드를 생산하기 위해서는 잠재적인 오류를 발생시키기 어려운 개발 환경을 구성하는 것이 좋다. 

이를 지원하기 위해 ES5부터 `strict mode`가 추가되었다. strict mode는 자바스크립트 언어의 문법을 더 엄격히 적용하여 오류 발생 가능성이 높거나 자바스크립트 엔진 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적 에러를 발생시킨다. Eslint 같은 린트 도구를 사용해도 strict mode와 유사한 효과를 얻을 수 있다. 린트 도구는 정적 분석 기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류만이 아니라 잠재적 오류까지 찾아내고 오류의 원인을 리포팅해주는 유용한 도구다.

린트 도구는 strict mode가 제한하는 오류는 물론 코딩 컨벤션을 설정 파일 형태로 정의하고 강제할 수 있기 때문에 더욱 강력한 효과를 얻을 수 있다. strict mode의 상위 호환이라고 볼 수 있다.

## 20.2 strict mode의 적용

전역의 선두 또는 함수 몸체의 선두에 `‘use strict’`를 추가한다. 

## 20.3 전역에 strict mode를 적용하는 것은 피하자

물론 strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다. 하지만 전역에 strict mode를 적용하는 것을 피해야 하는 이유는 외부 서드파티 라이브러리가 non-strict mode인 경우 사용을 못하게 될 수 있기 때문이다. 이러한 경우 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용한다.

```jsx
// 즉시 실행 함수의 선두에 strict mode 적용
(function(){
	'use strict';
	// Do something...
}());
```

## 20.4 함수 단위로 strict mode를 적용하는 것도 피하자

모든 함수에 일일이 strict mode를 적용할 수는 있지만 번거롭다. 또, strict mode가 적용된 함수가 참조할 함수 외부 컨텍스트에 strict 모드를 적용하지 않는다면 이 또한 문제가 발생할 수 있다. 

따라서 strict mode는 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직하다. 

## 20.5 strict mode가 발생시키는 에러

### 20.5.1 암묵적 전역

선언하지 않은 변수 참조 시 ReferenceError가 발생한다.

### 20.5.2 변수, 함수, 매개변수의 삭제

delete 연산자로 변수, 함수, 매개변수를 삭제하면 SyntaxError가 발생한다.

### 20.5.3 매개변수 이름의 중복

중복된 매개변수 이름을 사용하면 SyntaxError가 발생한다.

### 20.5.4 with 문의 사용

with문을 쓰지를 마라. 

## 20.6 strict mode 적용에 의한 변화

### 20.6.1 일반 함수의 this

strict mode에서 함수를 일반 함수로서 호출하면 this에 전역 객체가 아닌 undefined가 바인딩된다.

### 20.6.2 arguments 객체

strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

# 21장 빌트인 객체

## 21.1 자바스크립트 객체의 분류

1. 표준 빌트인 객체
    1. ECMAScript 사양에 정의된 객체 → 자바스크립트 실행 환경에 관계 없이 언제나 사용 가능
    2. 애플리케이션 전역의 공통 기능 제공
    3. 전역 객체의 프로퍼티로써 제공됨 → 전역 변수처럼 언제나 참조 가능
2. 호스트 객체
    1. ECMAScript 사양에 정의 X
    2. 자바스크립트 실행 환경에서 추가로 제공하는 객체
        1. 브라우저 환경 - Web APIs(DOM, BOM, Canvas, fetch, requestAnimationFrame, SVG 등)
        2. Node.js 환경 - Node.js 고유 API
3. 사용자 정의 객체
    1. 1, 2번과 같은 기본 제공되는 객체가 아닌 사용자가 직접 정의한 객체

## 21.2 표준 빌트인 객체

자바스크립트는 40여 개의 표준 빌트인 객체를 제공한다.

Object, String, Number, Boolean, Symbol, Date, Math, …

Math, Reflect, JSON을 제외한 표준 빌트인 객체는 모두 인스턴스를 생성할 수 있는 생성자 함수 객체다. 생성자 함수 객체인 표준 빌트인 객체는 프로토타입 메서드와 정적 메서드를 제공하고 생성자 함수 객체가 아닌 표준 빌트인 객체(Math, Reflect, JSON)는 정적 메서드만 제공한다.

생성자 함수인 표준 빌트인 객체가 생성한 인스턴스의 프로토타입([[Prototype]], __proto__로 이해해도 무방함)은 표준 빌트인 객체의 prototype 프로퍼티에 바인딩된 객체다. 표준 빌트인 객체는 인스턴스 없이도 호출 가능한 빌트인 정적 메서드를 제공한다.

## 21.3 원시값과 래퍼 객체

원시값은 객체가 아닌데도 해당 원시값과 관련된 생성자 함수의 프로토타입 메서드를 사용할 수 있다.  (null과 undefined는 래퍼 객체를 생성하지 않는다.)

```jsx
'hello'.toUpperCase() // Hello
```

이는 원시값에 대해 마침표 표기법 또는 대괄호 표기법으로 접근하면 자바스크립트 엔진이 일시적으로 원시값을 연관된 객체로 변환해 주기 때문이다. 이 임시 객체를 **래퍼 객체**라 한다. 예를 들어, 문자열에 대해 마침표 표기법으로 접근하면 그 순간 래퍼 객체인 String 생성자 함수의 인스턴스가 생성되고 문자열은 래퍼 객체의 [[StringData]] 내부 슬롯에 할당된다. 이때 문자열 래퍼 객체인 String 생성자 함수의 인스턴스는 String.prototype의 메서드를 상속받아 사용할 수 있다.

래퍼 객체의 처리가 종료되면 래퍼 객체의 [[StringData]] 내부 슬롯에 할당된 원시값으로 원래의 상태, 즉 식별자가 원시값을 갖도록 되돌리고 래퍼 객체는 가비지 컬렉션의 대상이 된다.

`

```jsx
// 1. 식별자 str은 문자열을 값으로 가지고 있다.
const str = 'hello';
// 2. 식별자 str은 암묵적으로 생성된 래퍼 객체를 가리킨다.
// 식별자 str의 값 'hello'는 래퍼 객체의 [[StringDtata]] 내부 슬롯에 할당된다.
// 래퍼 객체에 name 프로퍼티가 동적 추가된다.
str.name = 'Lee'
// 3. 식별자 str은 다시 원래의 문자열, 즉 래퍼 객체의 [[StringData]] 내부 슬롯에 할당된 원시값을 갖는다.
// 이때 2번에서 생성된 래퍼 객체는 아무도 참조하지 않는 상태이므로 가비지 컬렉션의 대상이 된다.

// 4. 식별자 str은 새롭게 암묵적으로 생성된(2번에서 생성된 래퍼 객체와는 다른) 래퍼 객체를 가리킨다.
// 새롭게 생성된 래퍼 객체에는 name 프로퍼티가 존재하지 않는다.
console.log(str.name) // undefined

// 5. 식별자 str은 다시 원래의 문자열, 즉 래퍼 객체의 [[StringData]] 내부 슬롯에 할당된 원시값을 갖는다.
// 이때 4번에서 생성된 래퍼 객체는 아무도 참조하지 않는 상태이므로 가비지 컬렉션의 대상이 된다.
console.log(typeof str, str) // string hello
```

즉, str의 값은 실제로 래퍼 객체에 대한 참조값으로 바뀌었다가 다시 원시값으로 돌아오는 과정을 거친다. 그렇다면, 그 원시값에 대한 참조값(메모리 주소)은 같을까?

문득 [값에 의한 전달에 관한 고찰](https://www.notion.so/4cae9817c4ca4f1a8e72a8066259b50b?pvs=21) 이 떠올라, 내 생각을 정리해보았다. 

참고) ES6에서 새롭게 도입된 원시값인 심벌도 래퍼 객체를 생성하지만, Symbol 함수를 통해 생성해야 하므로 다른 원시값과는 차이가 있다. Symbol은 생성자 함수가 아니다.

## 21.4 전역 객체

전역 객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않은 최상위 객체다.

브라우저 환경에서는 window(또는 self, this, frames), Node.js 환경에서는 global이 전역 객체를 가리킨다. ES11에서 전역 객체를 가리키던 식별자를 통일한 globalThis 식별자가 도입되었다.

전역 객체는 계층적 구조상 어떤 객체에도 속하지 않은, 모든 빌트인 객체(표준 빌트인 객체와 호스트 객체)의 최상위 객체다. 전역 객체가 최상위 객체라는 것은 프로토타입 상속 관계상에서 최상위 객체라는 의미가 아니다. 전역 객체 자신은 어떤 객체의 프로퍼티도 아니며 객체의 계층적 구조상 표준 빌트인 객체와 호스트 객체를 프로퍼티로 소유한다는 것을 말한다. 

### 전역 객체의 특징

1. 개발자가 의도적으로 생성할 수 없다.  ↔ 전역 객체를 생성할 수 있는 생성자 함수가 제공되지 않는다.
2. 전역 객체의 프로퍼티를 참조할 때 widnow(또는 global)를 생략할 수 있다.
3. 전역 객체의 프로퍼티
    1. 표준 빌트인 객체(Object, String, …)
    2. 호스트 객체(클라이언트 Web API, Node.js 호스트 API)
    3. var 키워드로 선언한 전역 변수 및 전역 함수, 암묵적 전역으로 선언된 전역 변수 및 전역 함수
4. 브라우저 환경의 모든 자바스크립트 코드는 전역 객체 window를 공유한다. 여러 개의 script 태그를 통해 자바스크립트 코드를 분리해도 window는 공유된다.
이는 분리되어 있는 자바스크립트 코드가 하나의 전역을 공유한다는 의미이다. 프로퍼티와 메서드는 전역 객체를 가리키는 식별자, 즉 window나 global을 생략하여 참조/호출할 수 있으므로 전역 변수와 전역 변수처럼 사용할 수 있다.

### 21.4.1 빌트인 전역 프로퍼티

빌트인 전역 프로퍼티는 전역 객체의 프로퍼티를 의미한다. 주로 애플리케이션 전역에서 사용하는 값을 제공한다.

1. Infinity
2. NaN
3. undefined

### 21.4.2 빌트인 전역 함수

빌트인 전역 함수는 애플리케이션 전역에서 호출할 수 있는 빌트인 함수로서 전역 객체의 메서드다.

- ~~eval~~
    
    ~~인수로 전달받은 문자열이 표현식이면 런타임에 문자열 코드를 평가하여 값을 생성하고, 표현식이 아닌 문이라면 문자열 코드를 런타임에 실행한다. 문자열 코드가 여러 개의 문으로 이루어져 있다면 모든 문을 실행한다.~~
    
    ```jsx
    eval('1 + 2;'); // -> 3
    ```
    
    ~~정리할 것도 없다. 보안에 취약하고 자바스크립트 엔진에 의해 최적화도 안되고 느리고 단점 투성이다. 그냥 eval 함수는 사용하지 마라.~~
    
1. isFinite - NaN은 false, null은 0이므로 true 반환
2. isNaN
3. parseFloat
4. parseInt 
    1. 두 번째 인수로 숫자 n 전달 ⇒ n진법으로 해석해 반환
        1. 해당 진수를 나타내는 문자가 아닌 문자가 존재할 때
            1. 첫 번째 문자가 해당 진수에서 해석 불가한 경우 → NaN
            2. Else → 해석 불가능한 값은 무시. 해석된 정수값만 반환한다.
    2. 16진수 리터럴(0x, 0X 시작) 해석해 10진수로  반환
    3. 첫 번째 인수의 문자열에 공백이 있는 경우 → 첫 번째 문자열만 해석하여 반환하며 앞뒤 공백은 무시됨. 해석 안되면 NaN
5. encodeURI
    1. 완전한 URI를 문자열로 전달받아 이스케이프 처리를 위해 인코딩한다. ‣ 
    2. ‣ 
    3. 쿼리 스트링 구분자(=, ?, &)를 인코딩 X
6. decodeURI
    1. 인코딩 된 URI를 인수로 전달받아 이스케이프 처리 이전으로 디코딩한다.
7. encodeURIComponent
    1. 인수로 전달된 문자열을 쿼리 스트링의 일부로 간주
    2. 쿼리 스트링 구분자(=, ?, &)까지 인코딩 O

## 21.4.3 암묵적 전역

```jsx
var x = 10; // var로 전역에서 선언한 변수도 window 객체의 프로퍼티가 된다. 단, delete 연산자로 삭제할 수 없다.
function foo() {
  // 스코프 체인 내에 어디서도 선언하지 않은 식별자에 값을 할당
  y = 20; // window.y = 20; window 객체의 프로퍼티가 되지만, delete 연산자로 삭제할 수 있다.
}
foo();
console.log(x + y); // 30
delete x;
delete y;
console.log(x); // 10
console.log(y); // ReferenceError: y is not defined
```

# 22장 this

## this란?

자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다. this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.

## this 바인딩은 렉시컬 스코프와 결정 시기가 다르다

this 바인딩은 함수 `호출` 시점에 결정되고, 렉시컬 스코프는 함수 정의가 평가되어 `함수 객체가 생성되는 시점`에 상위 스코프를 결정

## this 정리

|  | this 바인딩 |
| --- | --- |
| 일반 함수 | 일반 함수로 호출된 모든 함수 내부의 this에는 전역 객체가 바인딩 |
| 객체의 메서드 | 메서드 내부의 this는 메서드를 호출한 객체가 바인딩 |
| 생성자 함수 | new 연산자로 호출 시 생성할 인스턴스가 this에 바인딩 |
| 이벤트 핸들러 | 이벤트를 바인딩한 DOM 요소(=currentTarget)가 바인딩 |
| 화살표 함수 | 함수가 정의된 위치의 상위 스코프의 this가 그대로 바인딩 ⇒  lexical this |

### 객체의 메서드

객체의 메서드는 객체에 포함된 것이 아니라 독립적로 존재하는 별도의 객체이며, 함수 객체를 가리키고 있다.

[[HomeObject]] ‣ ‣ 

객체의 메서드를 화살표 함수로 정의하는 것은 피해야 한다.

**메서드를 정의할 때는 ES6 메서드 축약 표현으로 정의한 ES6 메서드를 사용하는 것이 좋다.**

```jsx
const person = {
  name: 'Lee',
  sayHi: () => console.log(`Hi ${this.name}`), // Hi undefined
  sayHello() {
    console.log(`Hello ${this.name}`); // Hello Lee // Good. ES6 메서드 축약 표현
  },
  sayFoo: function () {
    console.log(`Foo ${this.name}`); // Foo Lee, 다만 [[HomeObject]]가 다름. 이 메서드 내에서 super.method() 호출 시 의도한 대로 동작하지 않는다.
  },
};

person.sayBar = function () {
  console.log(`Bar ${this.name}`); // Bar Lee // 프로퍼티 동적 추가 시 ES6 메서드 정의를 사용할 수 없으므로 일반 함수 할당 // 이 경우는 [[HomeObject]] ??
};
```

## 생성자 함수

```jsx
const func = function () {
  console.log(this); // 호출 방법에 따라 다름
  setTimeout(function () {
    // 콜백이지만 일반 함수로 호출되므로 항상 window 바인딩, 화살표 함수라면 상위 스코프의 this와 일치
    console.log('callback this : ', this);
  }, 100);
};
func(); // 일반함수로 호출 -> 전역객체 window 바인딩

const obj = { func };
obj.func(); // 객체 메서드 -> 객체 바인딩 {func: ƒ}

new func(); // 생성자 함수 -> 생성될 인스턴스 가리킴 func {}
```

### 화살표 함수

class 필드에 할당한 화살표 함수는 프로토타입 메서드가 아니라 인스턴스 메서드가 된다. 따라서 ES6 메서드를 사용하는 것이 좋다.

## 이벤트 핸들러 내부의 this

### 이벤트 핸들러 어트리뷰트 방식

```html
 <button onclick="handleClick()">Click me!</button>
```

```jsx
function handleClick() {
	console.log(this); // window
}
```

```html
<!--이벤트 핸들러 어트리뷰트 값으로 함수 참조가 아닌 함수 호출문 등의 문을 할당한다. 이는 인수 전달을 용이하게 하기 위해 설계된 결과다.-->
 <button onclick="handleClick(this); console.log('hello!');">Click me!</button> 
```

```jsx
// 그냥 이렇게 onclick 몸체가 되는 것임
function onclick(event){
	handleClick(this); console.log('hello!');
}
```

```jsx
function handleClick(button) {
	console.log(button) // 이벤트를 바인딩한 button 요소
	console.log(this); // window
}
```

이벤트 핸들러 어트리뷰트 방식은 더는 사용하지 않는 것이 좋다. HTML과 자바스크립트는 관심사가 다르므로 혼재하는 것보다 분리하는 것이 좋다. 하지만 모던 자바스크립트에서는 이벤트 핸들러 어트리뷰트 방식을 사용하는 경우가 있다. CBD 방식의 Angular/React/Svelte/Vue.js 같은 프레임워크/라이브러리에서는 이벤트 핸들러 어트리뷰트 방식으로 이벤트를 처리한다. CBD에서는 HTML, CSS, 자바스크립트를 관심사가 다른 개별적인 요소가 아닌, 뷰를 구성하기 위한 구성 요소로 보기 때문에 관심사가 다르다고 생각하지 않는다.

```html
{ /* React */ }
<button onClick={handleClick}>Save</button>
```

### 이벤트 핸들러 프로퍼티 방식 & addEventListener 방식

두 가지 방식 모두 이벤트 핸들러 내부의 this는 이벤트를 바인딩한 DOM 요소(=currentTarget)를 가리킨다.

```html
<!DOCTYPE html>
<html lang="ko"><head><meta charset="UTF-8" /><meta name="viewport" content="width=device-width, initial-scale=1.0" /><title></title></head><body><button class="btn1">0</button><button class="btn2">0</button></body><script>
    const $button1 = document.querySelector('.btn1');
    const $button2 = document.querySelector('.btn2');
    //이벤트 캡처링, 버블링
    
    $button1.onclick = function (e) {
      console.log(this); // $button1
      console.log(e.currentTarget); // $button1
      console.log(this === e.currentTarget); // true
    };
    $button2.addEventListener('click', function (e) {
      console.log(this); // $button2
      console.log(e.currentTarget); // $button2
      console.log(this === e.currentTarget); // true
    });
  </script></html>
```

동일한 DOM 요소에 대해 이벤트 핸들러 프로퍼티 방식과 addEventListener 방식 2가지로 같은 이벤트를 등록하게 되면, 서로 아무런 영향도 주지 않고 두 개의 이벤트가 모두 발생하게 된다. addEventListener 방식으로 참조가 동일한 이벤트를 중복 등록한 경우에는 하나의 이벤트 핸들러만 등록된다.

‣ 

> 클래스에서 이벤트 핸들러를 바인딩 하는 경우 this에 주의해야 한다. - p.793
> 

### 22.2.4 Function.prototype.apply/call/bind 메서드에 의한 간접 호출

### apply & call

```jsx
Function.prototype.apply(thisArg[, argsArray])
Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```

두 메서드 모두 인수를 전달하는 모습만 다를 뿐, this로 사용할 객체와 필요한 인수를 전달하면서 함수를 호출하는 동일한 기능을 한다. 

```jsx
function convertArgsToArray(){
	console.log(arguments);
	const arr = Array.prototype.slice.call(arguments);
	// const arr = Array.prototype.slice.apply(arguments);
	console.log(arr)
	return arr;
}
convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

### bind

Function.prototype.bind 메서드는 apply & call 메서드와 달리 함수를 호출하지 않는다. 콜백 함수 전달에 매우 유용한 모습을 보인다.

```jsx
const person = {
	name: 'Lee',
	foo(callback){
		console.log(this);
		setTimeout(callback.bind(this), 100); // this 전달
	}
}
person.foo(function(){
	console.log(`Hi! My name is ${this.name}.`);
})
```

# 23장 실행 컨텍스트

자바스크립트의 동작 원리를 담고 있는 핵심 개념이다.

## 실행 컨텍스트를 바르게 이해하면 이해할 수 있는 것

1. 자바스크립트가 스코프 기반으로 식별자와 식별자에 바인딩된 값을 관리하는 방식
2. 호이스팅이 발생하는 이유
3. 클로저의 동작 방식
4. 태스크 큐와 함께 동작하는 이벤트 핸들러와 비동기 처리의 동작 방식

## 23.1 소스코드의 타입

아래와 같이 4가지 타입으로 구분하는 이유는 소스코드의 타입에 따라 실행 컨텍스트를 생성하는 과정과 관리 내용이 다르기 때문이다.

| 소스코드의 타입 | 설명 |
| --- | --- |
| 전역 코드 | 전역에 존재하는 소스코드. 전역에 정의된 함수, 클래스 등의 내부 코드는 포함되지 않는다. |
| 함수 코드 | 함수 내부에 존재하는 소스코드. 함수 내부에 중첩된 함수, 클래스 등의 내부 코드는 포함되지 않는다. |
| eval 코드 | 빌트인 전역 함수인 eval 함수에 인수로 전달되어 실행되는 소스코드. |
| 모듈 코드 | 모듈 내부에 존재하는 소스코드. 모듈 내부의 함수, 클래스 등의 내부 코드는 포함되지 않는다. |
1. 전역 코드
    
    전역 코드는 전역 변수를 관리하기 위해 최상위 스코프인 전역 스코프를 생성해야 한다. 그리고 var 키워드로 선언된 전역 변수와 함수 선언문으로 정의된 전역 함수를 전역 객체의 프로퍼티와 메서드로 바인딩하고 참조하기 위해 전역 객체와 연결되어야 한다. 이를 위해 전역 코드가 평가되면 전역 실행 컨텍스트가 생성된다.
    
2. 함수 코드
    
    함수 코드는 지역 스코프를 생성하고 지역 변수, 매개변수, arguments 객체를 관리해야 한다. 그리고 생성한 지역 스코프를 전역 스코프에서 시작하는 스코프 체인의 일원으로 연결해야 한다. 이를 위해 함수 코드가 평가되면 함수 실행 컨텍스트가 생성된다.
    
3. eval 코드
    
    eval 코드는 strict mode(엄격 모드)에서 자신만의 독자적인 스코프를 생성한다. 이를 위해 eval 코드가 평가되면 eval 실행 컨텍스트가 생성된다.
    
4. 모듈 코드
    
    모듈 코드는 모듈별로 독립적인 모듈 스코프를 생성한다. 이를 위해 모듈 코드가 평가되면 모듈 실행 컨텍스트가 생성된다.
    

## 23.2 소스코드의 평가와 실행

모든 소스코드는 실행에 앞서 평가 과정을 거치며 코드를 실행하기 위한 준비를 한다. 다시 말해, 자바스크립트 엔진은 소스코드를 2개의 과정, 즉 “소스코드의 평가”와 “소스코드의 실행” 과정으로 나누어 처리한다.

소스코드 평가 과정에서는 실행 컨텍스트를 생성하고 변수, 함수 등의 선언문만 먼저 실행하여 생성된 변수나 함수 식별자를 키로 실행 컨텍스트가 관리하는 스코프(렉시컬 환경의 환경 레코드)에 등록한다. 

소스코드 평가 과정이 끝나면 비로소 선언문을 제외한 소스코드가 순차적으로 실행되기 시작한다. 즉, 런타임이 시작된다. 이때 소스코드 실행에 필요한 정보, 즉 변수나 함수의 참조를 실행 컨텍스트가 관리하는 스코프에서 검색해 취득한다. 그리고 변수 값의 변경 등 소스코드의 실행 결과는 다시 실행 컨텍스트가 관리하는 스코프에 등록된다.

예를 들어, 다음과 같은 소스코드가 실행된다고 생각해보자.

```jsx
var x;
x = 1;
```

자바스크립트 엔진은 위 예제를 2개의 과정으로 나누어 처리한다. 먼저 소스코드 평가 과정에서 변수 선언문 var x;를 먼저 실행한다. 이때 생성된 변수 식별자 x는 실행 컨텍스트가 관리하는 스코프에 등록되고 undefined로 초기화된다.

소스코드 평가 과정이 끝나면 비로소 소스코드 실행 과정이 시작된다. 변수 선언문 var x;는 소스코드 평가과정에서 이미 실행이  완료되었다. 따라서 소스코드 실행 과정에서는 변수 할당문 x = 1;만 실행된다. 이때 x 변수에 값을 할당하려면 먼저 x 변수가 선언된 변수인지 확인해야 한다.

이를 위해 실행 컨텍스트가 관리하는 스코프에 x 변수가 등록되어 있는지 확인한다. 다시 말해, x 변수가 선언된 변수인지 확인한다.만약 x 변수가 실행 컨텍스트가 관리하는 스코프에 등록되어 있다면 x 변수는 선언된 변수, 즉 소스코드 평가 과정에서 선언문이 실행되어 등록된 변수다. x 변수가 선언된 변수라면 값을 할당하고 할당 결과를 실행 컨텍스트에 등록하여 관리한다.

## 23.3 실행 컨텍스트의 역할

다음 예제는 전역 코드와 함수 코드로 구성되어 있다. 자바스크립트 엔진이 이 예제를 어떻게 평가하고 실행할지 생각해보자.

```jsx
const x = 1;
const y = 2;

functiono foo(a){
	const x = 10;
	const y = 20;
	console.log(a + x + y); //
}
foo(100);
console.log(x + y);
```

- 내 생각
    1. 전역 소스코드 평가
        
        const x ; const y; → x, y를 전역 스코프에 등록 후 undefined 초기화
        
        foo 도 등록 + 초기화(함수 호이스팅)
        
    2. 전역 소스코드 실행
        1. x, y가 실행 컨텍스트에 등록된 식별자인지 검사 후 x = 1, y = 2  할당
        2. foo (100); 을 호출했으므로 함수 실행 컨텍스트로 전환
    3. 함수 소스코드 평가
        
        매개변수 a (= undefined로 초기화), const x ; const y; → x, y를 함수 스코프에 등록
        
    4. 함수 소스코드 실행
        
        a가 선언된 변수인지 확인 후 전달받은 인수로 초기화(a = 100)
        
        x, y가 선언된 변수인지 확인 후  x=10, y=20 할당
        
        console.log(a + x + y)에서 a, x, y를 함수 스코프에서 찾음 → 100, 10, 20이 할당되어있음
        
        따라서 a + x + y는 130으로 평가되어 출력됨
        
    5. 함수 소스코드 실행을 마쳐 전역 컨텍스트로 돌아와 소스코드 마저 실행
        1. 전역 스코프에 x, y가 등록되어 있는지 검사 → 1, 2
        2. x + y를 평가해 3 출력
1. 전역 코드 평가
    1. 선언문만 먼저 실행
    2. 전역 코드의 변수 선언문, 함수 선언문이 먼저 실행
    3. 생성된 전역 변수와 전역 함수가 실행 컨텍스트가 관리하는 전역 스코프에 등록
    4. var 키워드로 선언된 전역 변수와 함수 선언문으로 정의된 전역 함수는 전역 객체의 프로퍼티와 메서드가 됨
2. 전역 코드 실행
    1. 런타임 시작
    2. 전역 변수에 값 할당
    3. 함수 호출 ⇒ 전역 코드 실행 일시 중단하고 코드 실행 순서를 변경하여 함수 내부로 진입
3. 함수 코드 평가
    1. 매개변수와 지역 변수 선언문이 먼저 실행
    2. 생성된 매개변수와 지역 변수가 실행 컨텍스트가 관리하는 지역 스코프에 등록
    3. 또한 함수 내부에서 지역 변수처럼 사용할 수 있는 arguments 객체가 생성되어 지역스코프에 등록되고 this 바인딩도 결정된다
4. 함수 코드 실행
    1. 매개 변수와 지역 변수에 값이 할당되고 console.log 호출
    2. console을 스코프 체인을 통해 검색
    → 함수 코드의 지역 스코프는 상위 스코프인 전역 스코프와 연결되어야 한다.
    3. console 식별자는 스코프 체인에 등록되어 있지 않고, 전역 객체의 프로퍼티로 존재
    → 전역 객체의 프로퍼티가 마치 전역 변수처럼 전역 스코프를 통해 검색 가능해야 함
    4. log 프로퍼티를 console 객체의 프로토타입 체인을 통해 검색
    5. console.log 메서드에 인수로 전달된 표현식 a + x + y가 평가
    6. a + x + y는 스코프 체인을 통해 검색
    7. console.log 메서드 실행 종료
    → 함수 코드 실행 과정 종료되고 함수 호출 이전으로 되돌아가 전역 코드 실행을 계속함

이처럼 코드가 실행되려면 스코프를 구분하여 식별자와 바인딩된 값이 관리되어야 한다. 그리고 중첩 관계에 의해 스코프 체인을 형성하여 식별자를 검색할 수 있어야 하고, 전역 객체의 프로퍼티도 전역 변수처럼 검색할 수 있어야 한다.

또한 함수 호출이 종료되면 함수 호출 이전으로 되돌아가기 위해 현재 실행 중인 코드와 이전에 실행하던 코드를 구분하여 관리해야 한다.

이처럼 코드가 실행되려면 다음과 같이 스코프, 식별자, 코드 실행 순서 등의 관리가 필요하다.

1. 선언에 의해 생성된 모든 식별자(변수, 함수, 클래스 등)를 스코프를 구분하여 등록하고 상태 변화(식별자에 바인딩된 값의 변화)를 지속적으로 관리할 수 있어야 한다.
2. 스코프는 중첩 관계에 의해 스코프 체인을 형성해야 한다. 즉, 스코프 체인을 통해 상위 스코프로 이동하며 식별자를 검색할 수 있어야 한다.
3. 현재 실행 중인 코드의 실행 순서(예를 들어, 함수 호출에 의한 실행 순서 변경)를 변경할 수 있어야 하며 다시 되돌아갈 수도 있어야 한다.

이 모든 것을 관리하는 것이 바로 **실행 컨텍스트**다. 실행 컨텍스트는 소스코드를 실행하는 데 필요한 환경을 제공하고 코드의 실행 결과를 실제로 관리하는 영역이다.

좀 더 구체적으로 말해, 실행 컨텍스트는 식별자(변수, 함수, 클래스 등의 이름)를 등록하고 관리하는 스코프와 코드 순서 관리를 구현한 내부 메커니즘으로, **모든 코드는 실행 컨텍스트를 통해 실행되고 관리된다.**

식별자와 스코프는 실행 컨텍스트의 렉시컬 환경으로 관리하고 코드 실행 순서는 실행 컨텍스트 스택으로 관리한다.

## 23.4 실행 컨텍스트 스택

자바스크립트 엔진은 전역 코드를 평가하여 전역 실행 컨텍스트를 생성한다. 그리고 함수가 호출되면 함수 코드를 평가하여 함수 실행 컨텍스트를 생성한다. 이때 생성된 실행 컨텍스트는 스택 자료구좋로 관리되며, 이를 실행 컨텍스트 스택이라 한다.

```jsx
const x = 1;

function foo() {
	const y = 2;
	function bar(){
		const z = 3;
		console.log(x + y + z);
	}
	bar();
}
foo();
```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/177ed65e-c8b4-447c-8c86-0fb5e92c5b04/image.png)

1. 전역 코드의 평가와 실행
    
    자바스크립트 엔진은  먼저 전역 코드를 평가하여 전역 실행 컨텍스트를 생성하고 실행 컨텍스트에 푸시한다. 이때 전역 변수 x와 전역 함수 foo는 전역 실행 컨텍스트에 등록된다. 이후 전역 코드가 실행되기 시작하면 전역 변수 x에 값이 할당되고 전역 함수 foo가 호출된다.
    
2. foo 함수 코드의 평가와 실행
    
    전역 함수 foo가 호출되면 전역 코드의 실행은 일시 중단되고 코드의 제어권이 foo 함수 내부로 이동한다. 자바스크립트 엔진은 foo 함수 내부의 함수 코드를 평가하여 foo 함수 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 푸시한다. 이때 foo 함수의 지역 변수 y와 중첩 함수 bar가 foo 함수 실행 컨텍스트에 등록된다. 이후 foo 함수 코드가 실행되기 시작하여 지역 변수 y에 값이 할당되고 중첩 함수 bar가 호출된다
    
3. bar 함수 코드의 평가와 실행
    
    중첩 함수 bar가 호출되면 foo 함수 코드의 실행은 일시 중단되고 코드의 제어권이 bar 함수 내부로 이동한다. 자바스크립트 엔진은 bar 함수 내부의 함수 코드를 평가하여 bar 함수 실행 컨텍스트를 생성하고 실행 컨텍스트 스택에 푸시한다. 이때 bar 함수의 지역 변수 z가 bar 함수 실행 컨텍스트에 등록된다. 이후, bar 함수 코드가 실행되기 시작하여 지역 변수 z에 값이 할당되고 console.log 메서드를 호출 4한 이후, bar 함수는 종료된다.
    
4. foo 함수 코드로 복귀
    
    bar 함수가 종료되면 코드의 제어권은 다시 foo 함수로 이동한다. 이때 자바스크립트 엔진은 bar 함수 실행 컨텍스트를 실행 컨텍스트 스택에서 팝하여 제거한다. 그리고 foo 함수는 더 이상 실행할 코드가 없으므로 종료된다.
    
5. 전역 코드로 복귀
    
    foo 함수가 종료되면 코드의 제어권은 다시 전역 코드로 이동한다. 이때 자바스크립트 엔진은 foo 함수 실행 컨텍스트를 실행 컨텍스트 스택에서 팝하여 제거한다. 그리고 더 이상 실행할 전역 코드가 남아 있지 않으므로 전역 실행 컨텍스트도 실행 컨텍스트 스택에서 팝되어 실행 컨텍스트 스택에는 아무것도 남아있지 않게 된다.
    

이처럼 **실행 컨텍스트 스택은 코드의 실행 순서를 관리**한다. 소스코드가 평가되면 실행 컨텍스트가 생성되고 실행 컨텍스트 스택의 최상위에 쌓인다. 실행 컨텍스트 스택의 최상위에 존재하는 실행 컨텍스트는 언제나 현재 실행 중인 코드의 실행 컨텍스트다.

## 23.5 렉시컬 환경

렉시컬 환경Lexical Environment은 식별자와 식별자에 바인딩된 값, 그리고 상위 스코프에 대한 참조를 기록하는 자료구조로 실행 컨텍스트를 구성하는 컴포넌트다. 실행 컨텍스트 스택이 코드의 실행 순서를 관리한다면 렉시컬 환경은 스코프와 식별자를 관리한다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/54daaf58-165a-4c86-abf6-d7ad4f152526/image.png)

렉시컬 환경은 키와 값을 갖는 객체 형태의 스코프(전역, 함수, 블록 스코프)를 생성하여 식별자를 키로 등록하고 식별자에 바인딩된 값을 관리한다.

실행 컨텍스트는 아래처럼 LexicalEnvironment 컴포넌트와 VariableEnvironment 컴포넌트로 로 구성된다. 생성 초기의 실행 컨텍스트와 렉시컬 환경을 그림으로 표현하면 다음과 같다.

‣ 

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/4d284474-0e68-4644-9655-9d2b039a945b/image.png)

생성 초기에 LexicalEnvironment 컴포넌트와 VariableEnvironment 컴포넌트는 하나의 동일한 렉시컬 환경을 참조한다. 이후 몇 가지 상황을 만나면 VariableEnvironment 컴포넌트를 위한 새로운 레깃컬 환경을 생성하고, 이때부터 VariableEnvironment 컴포넌트와 LexicalEnvironment 컴포넌트는 내용이 달라지는 경우도 있다. 하지만 이 책에서는 strict mode와 eval 코드, try/catch문과 같은 특수한 상황은 제외하고 LexicalEnvironment 컴포넌트와 VariableEnvironment 컴포넌트도 구분하지 않고 렉시컬 환경으로 통일해 간략하게 설명한다. ‣ 

렉시컬 환경은 다음과 같이 두 개의 컴포넌트로 구성된다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/7179de34-b1a4-4ffa-92b9-267ad484dc54/image.png)

1. **환경 레코드 Environment Record**
    
    스코프에 포함된 **식별자**를 등록하고 등록된 식별자에 바인딩된 값을 관리하는 저장소다. 환경 레코드는 소스코드의 타입에 따라 관리하는 내용에 차이가 있다.
    
2. **외부 렉시컬 환경에 대한 참조 Outer Lexical Environment Reference**
    
    외부 렉시컬 환경에 대한 참조는 **상위 스코프**를 가리킨다. 이때 상위 스코프란 외부 렉시컬 환경, 즉 해당 실행 컨텍스트를 생성한 소스코드를 포함하는 상위 코드의 렉시컬 환경을 말한다. 외부 렉시컬 환경에 대한 참조를 통해 **단방향 링크드 리스트인 스코프 체인을 구현**한다.
    

## 23.6 **실행 컨텍스트의 생성과 식별자 검색 과정**

다음 예제를 통해 어떻게 실행 컨텍스트가 생성되고 코드 실행 결과가 관리되는지,

그리고 어떻게 실행 컨텍스트를 통해 식별자를 검색하는지 살펴보자.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/ab2d6887-93c6-47ca-95fc-4e8ab1e8ec65/image.png)

### 23.6.1 전역 객체 생성

전역 객체는 전역 코드가 평가되기 이전에 생성된다.

### 23.6.2 전역 코드 평가

소스코드가 로드되면 자바스크립트 엔진은 전역 코드를 평가한다.

전역 코드 평가는 다음과 같은 순서로 진행된다.

1. 전역 실행 컨텍스트 생성
2. 전역 렉시컬 환경 생성
    
    전역 렉시컬 환경을 생성하고 전역 실행 컨텍스트에 방닌딩한다.
    
    1. 전역 환경 레코드 생성
        1. 객체 환경 레코드 생성
            
            var 키워드로 선언한 전역 변수와 함수 선언문으로 정의한 전역 함수, [빌트인 전역 프로퍼티](https://www.notion.so/81a622654d49424b9b65e112fe862431?pvs=21)와 [빌트인 전역 함수](https://www.notion.so/81a622654d49424b9b65e112fe862431?pvs=21), [표준 빌트인 객체 관리](https://www.notion.so/81a622654d49424b9b65e112fe862431?pvs=21)
            
            var 키워드로 선언한 전역 변수와 함수 선언문으로 정의된 전역 함수는 객체 환경 레코드의 BindingObject를 통해 전역 객체의 프로퍼티와 메서드가 된다. 이때 등록된 식별자를 전역 환경 레코드의 객체 환경 레코드에서 검색하면 전역 객체의 프로퍼티를 검색하여 반환한다. 
            
        2. 선언적 환경 레코드 생성
            
            let, const 키워드로 선언한 전역 변수를 관리
            
    2. this 바인딩 - 전역 환경 레코드와 함수 환경 레코드에만 존재
        1. 전역 환경 레코드의 [[GlobalThisValue]] 내부 슬롯에 this(전역 객체)가 바인딩된다.
    3. 외부 렉시컬 환경에 대한 참조 설정

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/d3bbdb6e-b29d-4e90-a0c1-a2a89cc6af77/image.png)

위 과정을 거쳐 생성된 전역 실행 컨텍스트와 렉시컬 환경은 다음과 같다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/9943d554-dd16-498a-ab71-1f892288a735/image.png)

한글로 이해하면 쉬운 그림이다. 위 설명을 그대로 옮겨놓음.

- [ ]  한글로 번역해서 올리기

## 프로토타입 체인과 스코프 체인의 공통점

1. 단방향 링크드 리스트
2. 상위 체인에 대한 참조
3. 체인의 종점에서는 상위 체인에 대한 참조가 null이다

### 23.6.3 전역 코드 실행

### 식별자 결정

어느 스코프의 식별자를 참조해야 하는 지 결정하는 것

실행 중인 실행 컨텍스트에서 식별자를 검색하기 시작한다.

식별자가 존재하지 않으면 외부 렉시컬 환경에 대한 참조가 가리키는 렉시컬 환경(= 상위 스코프)으로 이동하여 식별자를 검색한다. 이것이 바로 **스코프 체인의 동작 원리**다. 스코프 체인의 종점인 전역 렉시컬 환경에서 검색할 수 없는 식별자는 참조 에러를 발생시킨다.

### 23.6.4 foo 함수 코드 평가

1. 함수 실행 컨텍스트 생성
2. 함수 렉시컬 환경 생성
    
    함수 렉시컬 환경을 생성하고 함수 실행 컨텍스트에 바인딩한다.
    
    1. 함수 환경 레코드 생성
    2. this 바인딩 - 전역 환경 레코드와 함수 환경 레코드에만 존재
        1. 함수 환경 레코드의 [[ThisValue]] 내부 슬롯에 [this](https://www.notion.so/81a622654d49424b9b65e112fe862431?pvs=21)가 바인딩된다.
    3. 외부 렉시컬 환경에 대한 참조 설정

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/897c56f3-b41e-40f6-be9e-d191b2176742/image.png)

함수는 어디서 호출했는지가 아니라 어디에 정의했는지에 따라 상위 스코프를 결정한다.

this와 헷갈리지 말자. this는 화살표 함수, 이벤트 핸들러 함수를 제외하고는 함수의 호출과 관련이 있다.

자바스크립트 엔진은 함수 정의를 평가하여 함수 객체를 생성할 때 현재 실행 중인 실행 컨텍스트의 렉시컬 환경, 즉 함수의 상위 스코프를 함수 객체의 내부 슬롯 **[[Environment]]**에 저장한다. 함수 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 할당되는 것은 바로 **[[Environment]]**에 저장된 렉시컬 환경의 참조다. 즉, 함수 객체의 내부 슬롯 **[[Environment]]**가 바로 렉시컬 스코프를 구현하는 메커니즘이다.

### 23.6.5 foo 함수 코드 실행

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/d2e0c78f-6f5c-430d-806c-4c9a6b2e19ca/image.png)

### 23.6.6 bar 함수 코드 평가

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/bbe8b759-288d-4211-8769-def1cacca05a/image.png)

### 23.6.7 bar 함수 코드 실행 - console.log도 생각

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/2b996434-3ee8-4d4a-b617-e97750b5dd88/image.png)

### 23.6.8 bar 함수 코드 실행 종료

console.log 메서드가 호출되고 종료하면 더는 실행할 코드가 없으므로 bar 함수 코드의 실행이 종료된다.이때 실행 컨텍스트 스택에서 bar 함수 실행 컨텍스트가 팝되어 제거되고 foo 실행 컨텍스트가 실행 중인 실행 컨텍스트가 된다. 

bar 함수 실행 컨텍스트가 소멸되었다 하더라도 만약 bar 함수 렉시컬 환경을 누군가 참조하고 있다면 bar 함수 렉시컬 환경은 소멸하지 않는다.

### 23.6.9 foo 함수 코드 실행 종료

bar 함수가 종료하면 더 이상 실행할 코드가 없으므로 foo 함수 코드의 실행이 종료된다. 이때 실행 컨텍스트 스택에서 foo 함수 실행 컨텍스트가 팝되어 제거되고 전역 실행 컨텍스트가 실행 중인 실행 컨텍스트가 된다.

### 23.6.10 전역 코드 실행 종료

foo 함수가 종료되면 더는 실행할 전역 코드가 없으므로 전역 코드의 실행이 종료되고 전역 실행 컨텍스트도

실행 컨텍스트 스택에서 팝되어 실행 컨텍스트 스택에는 아무것도 남아있지 않게 된다.

## 23.7 실행 컨텍스트와 블록 레벨 스코프

var 키워드로 선언한 변수는 오로지 함수의

코드 블록만 지역 스코프로 인정하는 함수 레벨 스코프를 따른다. 하지만 let, const 키워드로 선언한 변수는 모든 코드 블록(함수, if 문, for 문, while 문, try/catch 문 등)을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다.

```jsx
let x = 1;

if (true){
	let x = 10;
	console.log(x); // 10
}

console.log(x); // 1
```

if 문의 코드 블록 내에서 let 키워드로 변수가 선언되었다. 따라서 if 문의 코드 블록이 실행되면 if 문의 코드 블록을 위한 블록 레벨 스코프를 생성해야 한다. 이를 위해 선언적 환경 레코드를 갖는 렉시컬 환경을 새롭게 생성하여 기존의 전역 렉시컬 환경을 교체한다. 이때 새롭게 생성된 if 문의 코드 블록을 위한 렉시컬 환경의 외부 렉시컬 환경에 대한 참조는 if 문이 실행되기 이전의 전역 렉시컬 환경을 가리킨다.

![{9D082C76-52C5-43F5-BAF8-4D3568FE1F89}.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/692f3291-6cea-4000-9121-690642040920/9D082C76-52C5-43F5-BAF8-4D3568FE1F89.png)

![{85AFF1CA-A15F-45C2-B8D7-589B5ECDD65B}.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/dbc49794-b983-4f69-8afa-b5c54d5f9b82/e111d19e-1b9a-4725-8590-7cc363fd32f2/85AFF1CA-A15F-45C2-B8D7-589B5ECDD65B.png)

# 24장 클로저

자바스크립트만의 고유한 특성이 아니고, 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어(하스켈, 리스프, 얼랭, 스칼라 등)에서 사용되는 중요한 특성이다.

> MDN  - 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.
> 

```jsx
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }

  innerFunc();
}

outerFunc();
```

중첩 함수 innerFunc 내부에서 자신을 포함하고 있는 외부 함수 outerFunc의 변수 x에 접근이 가능하다. 하지만 만약 innterFunc 함수가 outerFunc 함수 내부에서 정의된 중첩 함수가 아니라면, 내부에서 호출한다고 하더라도 변수 x에 접근이 불가능하다. ⇒ 자바스크립트가 렉시컬 스코프를 따르기 때문

# 24.1 렉시컬 스코프

> 렉시컬 스코프 (Lexical scope)
> 
> 
> 자바스크립트 엔진은 함수가 정의된 위치에 따라 상위 스코프를 결정한다.
> 

```jsx
const x = 1;

function foo() {
  const x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```

함수의 상위 스코프를 결정한다는 말은 곧, “**렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조값을 결정한다.”** 는 말과 같다.

**렉시컬 환경의 “외부 렉시컬 환경에 대한 참조”에 저장할 참조값 (상위 스코프에 대한 참조)은 함수 정의가 평가되는 시점에 함수가 정의된 환경 (위치)에 의해 결정된다.**

# 24.2 함수 객체의 내부 슬롯 [[Environment]]

함수는 자신의 내부 슬롯 [[Environment]]에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장한다.

이 내부 슬롯에 저장된 현재 실행 중인 실행 컨텍스트의 렉시컬 환경의 참조가 바로 상위 스코프.

동시에 자신이 호출되었을 때 생성될 함수 렉시컬 환경의 “외부 렉시컬 환경에 대한 참조”에 저장될 참조값.

함수 객체는 내부 슬롯 [[Environment]]에 저장한 렉시컬 환경의 참조, 즉 상위 스코프를 **“자신이 존재하는 한 기억한다.”**

# 24.3 클로저와 렉시컬 환경

```jsx
const x = 1;

// ①
function outer() {
  const x = 10;
  const inner = function () { console.log(x); }; // ②
  return inner;
}

// outer 함수를 호출하면 중첩 함수 inner를 반환한다.
// 그리고 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 팝되어 제거된다.
const innerFunc = outer(); // ③
innerFunc(); // ④ 10
```

외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다.

이러한 중첩 함수를 클로저 라고 한다.

상위 예시에서, outer 함수의 실행 컨텍스트가 실행 컨텍스트 스택에서 제거되더라도 outer 함수의 렉시컬 환경은 소멸하지 않는다.

inner 함수의 [[Environment]] 내부 슬롯에 의해 참조되고 있고, innter 함수는 전역 변수 innerFunc에 의해 참조되고 있으므로, 가비지 컬렉팅이 되지 않기 때문.

이론적으로 자바스크립트의 모든 함수는 상위 스코프를 기억하기 때문에 클로저.

하지만 상위 스코프의 어떠한 식별자도 참조하지 않는다면 모던 브라우저에 의해 최적화되어 상위 스코프를 기억하지 않는다.

> 클로저는 중첩 함수가 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우에만 한중하는 것이 일반적.
> 

> 자유 변수 (free variable)
> 
> 
> 클로저에 의해 참조되는 상위 스코프의 변수.
> 
> 클로저란 “함수가 자유 변수에 대해 닫혀있다.” 라고 표현이 가능하다.
> 
> 더 쉽게 말하면 “자유 변수에 묶여있는 함수” 라고 할 수 있다.
> 

# 24.4 클로저의 활용

상태를 안전하게 은닉하고 특정 함수에게만 변경을 허용하기 위해 사용한다.

```jsx
const counter = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저인 메서드를 갖는 객체를 반환한다.
  // 객체 리터럴은 스코프를 만들지 않는다.
  // 따라서 아래 메서드들의 상위 스코프는 즉시 실행 함수의 렉시컬 환경이다.
  return {
    // num: 0, // 프로퍼티는 public하므로 은닉되지 않는다.
    increase() {
      return ++num;
    },
    decrease() {
      return num > 0 ? --num : 0;
    }
  };
}());

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

```jsx
const Counter = (function () {
  // ① 카운트 상태 변수
  let num = 0;

  function Counter() {
    // this.num = 0; // ② 프로퍼티는 public하므로 은닉되지 않는다.
  }

  Counter.prototype.increase = function () {
    return ++num;
  };

  Counter.prototype.decrease = function () {
    return num > 0 ? --num : 0;
  };

  return Counter;
}());

const counter = new Counter();

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

increase, decrease 함수는 모두 클로저이다.

자신의 함수 정의가 평가되어 함수 객체가 될 때 실행 중인 실행 컨텍스트 (즉시 실행 함수)의 렉시컬 환경을 기억한다.

즉, num 변수는 increase, decrease 메서드만 변경할 수 있다.

```jsx
// 함수를 인수로 전달받고 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
function makeCounter(aux) {
  // 카운트 상태를 유지하기 위한 자유 변수
  let counter = 0;

  // 클로저를 반환
  return function () {
    // 인수로 전달 받은 보조 함수에 상태 변경을 위임한다.
    counter = aux(counter);
    return counter;
  };
}

// 보조 함수
function increase(n) {
  return ++n;
}

// 보조 함수
function decrease(n) {
  return --n;
}

// 함수로 함수를 생성한다.
// makeCounter 함수는 보조 함수를 인수로 전달받아 함수를 반환한다
const increaser = makeCounter(increase); // ①
console.log(increaser()); // 1
console.log(increaser()); // 2

// increaser 함수와는 별개의 독립된 렉시컬 환경을 갖기 때문에 카운터 상태가 연동하지 않는다.
const decreaser = makeCounter(decrease); // ②
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

```jsx
// 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
const counter = (function () {
  // 카운트 상태를 유지하기 위한 자유 변수
  let counter = 0;

  // 함수를 인수로 전달받는 클로저를 반환
  return function (aux) {
    // 인수로 전달 받은 보조 함수에 상태 변경을 위임한다.
    counter = aux(counter);
    return counter;
  };
}());

// 보조 함수
function increase(n) {
  return ++n;
}

// 보조 함수
function decrease(n) {
  return --n;
}

// 보조 함수를 전달하여 호출
console.log(counter(increase)); // 1
console.log(counter(increase)); // 2

// 자유 변수를 공유한다.
console.log(counter(decrease)); // 1
console.log(counter(decrease)); // 0
```

# 24.5 캡슐화와 정보 은닉

> 캡슐화 (Encapsulation)
> 
> 
> 객체의 상태를 나타내는 프로퍼티와, 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것.
> 

정보 은닉은 외부에 공개할 필요가 없는 구현의 일부를 외부에 공개되지 않도록 감추어 적절치 못한 접근으로부터 객체의 상태가 변경되는 것을 방지해 정보를 보호하고, 객체간의 상호의존성(결합도)를 낮추는 효과가 있다.

자바스크립트는 public, privated, protected와 같은 접근 제한자를 제공하지 않는다.

따라서 자바스크립트의 모든 객체는 기본적으로 public하다.

‣ 

- 1
    
    다음 예제에서 _age 변수는 내부 변수이므로 private하다.
    
    하지만 Person 객체가 생성될 때 마다 중복으로 생성된다.
    
    ```jsx
    function Person(name, age) {
      this.name = name; // public
      let _age = age;   // private
    
      // 인스턴스 메서드
      this.sayHi = function () {
        console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
      };
    }
    
    const me = new Person('Lee', 20);
    me.sayHi(); // Hi! My name is Lee. I am 20.
    console.log(me.name); // Lee
    console.log(me._age); // undefined
    
    const you = new Person('Kim', 30);
    you.sayHi(); // Hi! My name is Kim. I am 30.
    console.log(you.name); // Kim
    console.log(you._age); // undefined
    ```
    
- 2
    
    다음 예제에서 Person.prototype.sayHi 메서드는 즉시 실행 함수의 지역 변수 _age를 참조할 수 있는 클로저이므로, 중복 생성 없이 private 변수에 접근이 가능하다.
    
    하지만 여러 개의 인스턴스를 생성할 경우 변수의 상태가 유지되지 않는다.
    
    해당 메서드는 단 한번씩만 생성되는 클로저이기 때문.
    
    ```jsx
    const Person = (function () {
      let _age = 0; // private
    
      // 생성자 함수
      function Person(name, age) {
        this.name = name; // public
        _age = age;
      }
    
      // 프로토타입 메서드
      Person.prototype.sayHi = function () {
        console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
      };
    
      // 생성자 함수를 반환
      return Person;
    })();
    
    const me = new Person('Lee', 20);
    me.sayHi(); // Hi! My name is Lee. I am 20.
    console.log(me.name); // Lee
    console.log(me._age); // undefined
    
    const you = new Person('Kim', 30);
    you.sayHi(); // Hi! My name is Kim. I am 30.
    console.log(you.name); // Kim
    console.log(you._age); // undefined
    me.sayHi(); // Hi! My name is Lee. I am 30.
    
    ```
    

이처럼 자바스크립트는 정보 은닉을 완벽하게 지원하지 않는다.

# 24.6 자주 발생하는 실수

다음의 예제에서 for문 안에 있는 var i 변수는 함수 스코프만을 따르기 때문에 전역 변수이다.

따라서 funcs 배열 안에 저장된 3개의 함수는 모두 3을 반환한다.

```jsx
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () { return i; }; // ①
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // ②
}
```

클로저를 활용하면 다음과 같이 수정할 수 있다.

즉시 실행 함수가 반환한 중첩 함수는 자신의 상위 스코프를 기억하는 클로저이다.

매개변수 id는 즉시 실행 함수가 반환한 중첩 함수에 묶여있는 자유 변수가 되어 그 값이 유지된다.

```jsx
var funcs = [];

for (var i = 0; i < 3; i++){
  funcs[i] = (function (id) { // ①
    return function () {
      return id;
    };
  }(i));
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

const, let 키워드를 사용하면 더 깔끔하게 해결할 수 있다.

const, let 변수는 블록 레벨 스코프를 따르기 때문에 반복문이 돌 때마다 새로운 렉시컬 스코프를 생성한다.

```jsx
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () { return i; };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]()); // 0 1 2
}
```

또는 함수형 프로그래밍 기법인 고차 함수를 사용할 수 있다.

```jsx
// 요소가 3개인 배열을 생성하고 배열의 인덱스를 반환하는 함수를 요소로 추가한다.
// 배열의 요소로 추가된 함수들은 모두 클로저다.
const funcs = Array.from(new Array(3), (_, i) => () => i); // (3) [ƒ, ƒ, ƒ]

// 배열의 요소로 추가된 함수 들을 순차적으로 호출한다.
funcs.forEach(f => console.log(f())); // 0 1 2
```
