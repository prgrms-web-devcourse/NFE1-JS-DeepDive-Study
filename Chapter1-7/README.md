# 모던 자바스크립트 딥다이브 스터디 1회: Chapter1-7 질의응답

## 질문 목록

- [Issue 🎯](https://github.com/prgrms-web-devcourse/NFE1-JS-DeepDive-Study/issues/1)

## 2장

<aside>
💡 자바스크립트를 객체 지향 언어라고 말할 수 있는 증거가 무엇이 있는가?(김민정)

</aside>

- 김민정
  - 객체 지향 언어
    - 클래스 기반 객체 지향 언어
    - 프로토타입 기반 객체 지향 언어
  - 특징
    - 캡슐화
    - 상속
    - 추상화
    - 다형성
- 김민석
  - 프로토타입 기반 상속
  - 객체와 클래스 생성 가능
  - 캡슐화, 다형성 구현 가능

<aside>
💡 자바스크립트는 인터프리터 언어지만, 인터프리터와 컴파일러의 장점을 결합했다. 책에는 결국 컴파일러 언어라고 할 수는 없다고 적혀있고, 인터프리터 언어라고 명시되어 있다. 하지만 컴파일러와 인터프리터의 기술적 구분이 점차 모호해져 가고 있다고도 적혀있다. 뭔가 이것도 아니고 저것도 아닌 느낌이라 질문에 남겨두겠습니다. 자바스크립트는 대체 어떤 언어일까요ㅜㅜ(이성훈)

</aside>

- 김민정
  - 컴파일: 특정 언어로 작성된 코드를 다른 언어(기계어)로 바꾸는 것
  - 인터프리트: 코드를 읽어나가며 해석하여 실행하는 것
  - 자바스크립트 실행하기 위해선 해석 엔진이 필요
    - V8 엔진: 2개의 컴파일러 사용
    - Monkey 엔진: 컴파일러(JIT=Just in Time) 사용
  - 컴파일 > 인터프리트 작동
  - 즉, 기본적으로 인터프리터 언어의 성질을 가지지만 성능 최적화를 위해 컴파일러 언어 성질도 가진다
- 김민석
  - 기본적 인터프리터
  - 특성에 따라 컴파일러 추가
  - 현재 하이브리드 언어!

## 3장

<aside>
💡 개발 환경으로서 브라우저 환경만의 특징은 뭐가 있을까?

</aside>

- 김민정
  - 웹 표준: W3C 등애서 정의한 웹 표준 준수 필요
  - 브라우저 호환성: 크로수 브라우저 호환성 고려 개발 필요
- 김민석
  - 돔 조작
  - 윈도우, document 객체 사용 가능!
  - 웹 API 접근

## 4장

<aside>
💡 변수에 값을 할당할 때는 이전 값 undefined가 저장된 메모리 공간을 지우고 그 공간에 할당 값을 새롭게 저장하는 것이 아니라 새로운 메모리 공간을 확보하고 그곳에 할당 값을 저장하는 이유는?

</aside>

- 김민정
  - 참조 투명성 유지
    - 같은 입력이 같은 출력 생성
    - 기존 메모리 공간에 새로운 값을 덮어쓰면, 다른 변수나 상수에 의도치 않은 영향 주기 가능
  - 가비지 컬렉션 원활하게 작동
    - 기존의 메모리 공간을 지우고 새로운 값을 저장하는 방식이라면, 가비지 컬렉션 이점 활용 불가능
    - 새로운 메모리 공간 할당을 통해 가비지 컬렉션이 이전 메모리를 관리할 수 있음
  - 멀티 스레드 환경 잠재적 문제 방지
- 김민석
  - 가비지 컬렉션
  - 메모리 파편화

<aside>
💡 자바스크립트는 왜 호이스팅을 할까?

</aside>

- 김민정
  - 실행 방식 명확화게 함
    - 컴파일 단계: 변수-함수 선언이 컴파일러에 의해 인식
    - 인터프리트 단계: 변수-함수 값 할당
  - 유연한 코딩 허용
  - 잠재적 오류 방지
- 김민석
  - 코드 실행 전 변수 함수 처리를 해서 선언 유연성 제공
  - 헷갈리지 않게 호환성 유지

## 6장

<aside>
💡 자바스크립트가 동적타입언어를 사용하는 이유는?

</aside>

- 김민정
  - 초기 웹 개발 필요성
  - 자바스크립트는 웹 페이지 동적 요소를 쉽게 추가하기 위해 개발됨
  - 그 당시 빠르게 동작하는 간단한 스크립트 필요
  - 유연성
    - 다양한 데이터 처리 가능
  - 빠른 개발
    - 타입 엄격 관리 필요하지 않아 빠르게 구현-테스트 가능
    - 데이터 구조나 변수 타입 쉽게 변경 가능
  - 경량 언어
- 김민석
  - 타입 지정 시간 줄인다
  - 다양한 데이터 타입 처리 좋다
- 이성훈
  - 타입스크립트: 더 빠르고 정확하게 하려고 나왔다!
    ⇒ 동적타입언어 단점때문에 등장

<aside>
💡 심벌(Symbol) 타입이 실제로 사용되는 예시가 더 있을까요?

</aside>

- 김주영

  - **객체의 비공개 속성(Private Properties) 생성:**심벌을 이용해 객체의 비공개 속성을 정의할 수 있다. 이렇게 하면 일반적인 문자열 키와 달리 접근이 제한된 속성을 만들 수 있다.

  ```jsx
  const privateId = Symbol("id");
  const user = {
    name: "Alice",
    [privateId]: 12345,
  };

  console.log(user[privateId]); // 12345
  console.log(user.id); // undefined (접근 불가)
  ```

- 김민정

  - 고유한 객체 속성 키

    - 다른 코드에서 접근 또는 수정 불가능

    ```
    const uniqueId = Symbol("id");

    const user = {
      name: "Alice",
      age: 30,
      [uniqueId]: 12345,
    };

    console.log(user.name); // "Alice"
    console.log(user.age); // 30
    console.log(user[uniqueId]); // 12345

    // user 객체를 열거할 때, Symbol 키는 포함되지 않음
    for (let key in user) {
      console.log(key); // "name", "age"만 출력
    }

    console.log(Object.keys(user)); // ["name", "age"]
    console.log(Object.getOwnPropertySymbols(user)); // [Symbol(id)]

    ```

    - 상수와 같은 고유 값

    ```
    const STATUS_PENDING = Symbol("pending");
    const STATUS_COMPLETED = Symbol("completed");
    const STATUS_FAILED = Symbol("failed");

    function getStatus(status) {
      switch (status) {
        case STATUS_PENDING:
          return "Pending...";
        case STATUS_COMPLETED:
          return "Completed!";
        case STATUS_FAILED:
          return "Failed!";
        default:
          return "Unknown status";
      }
    }

    console.log(getStatus(STATUS_PENDING)); // "Pending..."
    console.log(getStatus(STATUS_COMPLETED)); // "Completed!"

    ```

<aside>
💡 정적 타입 언어와 동적 타입 언어의 명확한 장단점이 있을까요?

</aside>

- 김민정
  - 정적 타입 언어
  - 컴파일시 타입 결정 > 안전성 높고, 실행 속도 빠름
  - 타입 미리 결정해 수정시 번거로움
  - 동적 타입 언어
    - 런타임시 타입 결정 > 유연성 높고, 개발 속도 빠름
    - 타입 미리 결정하지 않아 예상하지 못한 타입 에러 발생 가능
- 김민석
  - 동적
    - 타입 추론해서 그로 인해 발생하는 오류가 있을 수 있음!
- 김주영
  - 타입스크립트: 동적 타입 언어 .. superset 개념 도입 > 유연함 제공
  - 정적
    - 장점
      - 컴파일 오류 발견 가능 = 런타임 오류 줄이기 가능 => 안전한 코드 작성 가능
      - 타입 명확하게 선언 => 코드의 의도를 파악하기 쉬움
    - 단점
      - 개발 속도 느림
      - 유연성 느림
  - 동적
    - 장점
      - 개발 속도 빠르게 가능
      - 유연성
    - 단점
      - 코드 모호해짐 => 코드 의도 파악하기 어려움
      - 런타임에 타입 결정해 성능 저하
