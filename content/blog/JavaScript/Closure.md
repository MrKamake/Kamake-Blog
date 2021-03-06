---
title: 'Closure'
date: 2019-7-12 22:23:33
category: 'Javascript'
---

자바스크립트에서 클로저는 생성(선언)되는 순간 어떤 함수가 본인이 선언된 주변 환경을 (변경된 주위 환경까지도) **지속적으로** 기억하는 것을 의미한다. 해당 함수가 실행되는 위치가 어딘지는 관계없다. 선언된 곳을 기준으로 주위를 접근할 수 있다. 클로저를 알아보기 전에 실행컨텍스트를 알아본다면 더 쉽게 이해 할 수 있다.

> ## 실행 컨텍스트

  실행 컨텍스트는 자바스크립트의 핵심 원리다. 코드 독해와 디버깅을 위해 습득하고 있어야 한다. 실행 될 코드를 형상화하고 구분하는 추상적인 개념이라고 정의한다. 쉽게 표현한다면 실행 가능한 모드가 되기 위해 필요한 환경이다.

  코드를 실행하기 위해 필요한 정보들은 변수(전역변수, 지역변수, 매개변수, 객체의 프로퍼티), 변수의 유효범위, this가 있다.
  이러한 정보들을 형상화하고 구분하기 위해서 엔진은 실행 컨텍스트를 물리적 객체의 형태로 관리한다.

> ### 실행컨텍스트의 3가지 객체

- Variable Object

  실행에 필요한 여러 정보들을 담을 변수 객체를 생성한다. 실행 될때 엔진에 의해 참조되며 코드에서는 접근할 수 없다.
  => 변수, 매개변수(parameter)와 인수 정보(arguments), 함수 선언(함수표현식은 제외)
  전역 컨텍스트일 경우 VO는 유일하고 모든 전역 변수, 전역 함수 등을 포함하는 전역 객체를 가리킨다.
  함수 컨텍스트의 경우 VO는 활성객체(변수객체)를 가리킨다. 매개변수와 인수들의 정보를 배열의 형태로 담고있는 객체인 arguments object가 추가된다.

- Scope Chain

  스코프 체인은 일종의 리스트로서 전역 객체와 중첩된 함수의 스코프의 레퍼런스를 **차례로** 저장하고 있다. 현재 실행 컨텍스트의 활성 객체를 선두로 하여 순차적으로 상위 컨텍스트의 활성 객체를 가리킨다. 마지막 리스트는 전역 객체를 가리킨다.
  쉽게 말해 함수 객체가 처음 실행될 당시 실행 컨텍스트가 무엇인지를 생각해야하는 것이고 없다면 상위 컨텍스트로 옮겨가면서 찾아가는 것이다.
  요약하자면 **스코프 체인 = 현재 실행 컨텍스트의 변수 객체 + 상위 컨텍스트의 스코프 체인**

- this value

  this에 할당되는 값은 함수 호출 패턴에 의해 결정된다.

> ## 추가적 설명

  생성된 함수 객체는 `[[Scopes]]`프로퍼티를 가지게 된다. `[[Scopes]]`프로퍼티는 함수 객체만이 소유하는 내부 프로퍼티로서 함수 객체가 실행되는 환경을 가리킨다. 따라서 현재 실행 컨텍스트의 스코프 체인이 참조하고 있는 객체를 값으로 설정한다. 내부 함수의 `[[Scopes]]`프로퍼티는 자신의 실행 환경과 자신을 포함하는 외부 함수의 실행 환경과 전역 객체를 가리키는데 이때 자신을 포함하는 외부 함수의 실행 컨텍스트가 소멸하여도 `[[Scopes]]`프로퍼티가 가리키는 외부 함수의 실행환경은 소멸하지 않고 참조할 수 있다. 이것이 **클로저**이다.
  -> **이미 생명 주기가 끝난 외부함수의 변수를 참조 하는 함수를 클로저라 한다.**
  (실행 컨텍스트 관점으로 설명하면, 내부함수가 유효한 상태에서 외부함수가 종료하여 외부함수의 컨텍스트가 반환되어도, 외부함수 실행컨텍스트 내의 활성 객체는 내부함수에 의해 참조되는 한 유효하다. 그래서 내부함수가 스코프체인을 통해 참조할 수 있다.)
  클로저는 자신이 생성(선언)될때의 환경을 기억해야 하므로 메모리 차원에서 손해를 볼 수 있다.
  클로저가 가장 유용하게 사용되는 상황은 **현재 상태를 기억하고 변경된 최신 상태를 유지**하는 것이다.

```js
function outerFunc() {
  var x = 1 // <- 자유 변수
  return function() {
    // <- 클로저
    /* x와 arguments를 활용한 로직*/
  }
}

var newFunc = outerFunc()
/* outerFunc 실행 컨텍스트가 끝났다 */
newFunc()
```

실행 컨텍스트가 끝났음에도 불구하고 읽기 및 쓰기가 가능하다.

> ### 클로저 사용시 주의 사항

- 클로저의 프로퍼티 값이 쓰기 가능하므로 그 값이 여러번 호출로 항상 변할 수 있다.
- 하나의 클로저가 여러함수 객체의 스코프 체인에 들어가 있는 경우도 있다.
- 루프 안에서 클로저를 활용 할때는 주의해야한다.

> ## 참고

- map은 closure을 이용해서 각각의 변수를 새로만든 함수와 연결시켜준다. 비동기의 결과를 동기적으로 실행해야할 경우 클로저를 적용해서 새로운 스코프를 만들어서 원하는 결과값을 얻을 수 있다.

- 자바스크립트에서 함수는 일급 객체로 취급한다. 함수를 다른 함수의 인자로 넘길 수 있고, return 으로 함수 통째로 반환 받을 수 있다.

- 내부 함수에서 접근하는 변수 대부분이 스코프 체인의 첫번째 객체가 아닌 이후의 객체에 존재할 가능성이 높다. 성능 문제를 유발시키는 여지가 있다. 게다가 클로저를 사용한 코드가 그렇지 않은 코드보다 메모리 부담이 많아진다. 그렇다고 클로저라는 자바스크립트의 강력한 기능을 쓰지 않는 것은 슬픈일이다.라고 한다. *영리하게 사용하는 지혜*가 필요하다.
