---
layout: post
title:  "Chapter 01. Node.js 플랫폼에 오신 것을 환영합니다"
date:   2019-01-08 04:19:28 +0900
categories: node, nodejs, nodejs-design-pattern, design-pattern
---
# 01. Node.js basic
* 이벤트루프 기반의 논블록킹 I/O -> 동시성 고려하지 않아도 되기에 개발 편한 장점
* 모듈 패턴과 단순성, 최소화의 원리 

## node.js 철학
#### 경량 모듈
* 유닉스의 철학에 뿌리
  - 작은 것이 아름답다, SPA
* 장점
  - 재사용 좋다. 이해하기 쉽다. 사용하기 쉽다
  - 테스터블 코드, 구현을 단순히 하여, 유지보수가 편하다
#### 외부 인터페이스로 적게 노출
* 모듈의 크기와 범위가 작다. => 최소한의 기능을 노출한다 => API 사용성이 쉽다 => 잘못된 사용에 노출될 우려가 적다.
* 모듈을 정의하는 일반적인 패턴?
  - 함수, 생성자와 같이 하나의 핵심 기능 표현
  - 고급 기능, 보조 기능은 노출된 함수나 생성자의 속성이 되도록 한다.
  - **사용자는 중요한 내용과 부수적인 내용 구분이 가능하다.**
#### KISS (Keep It Simple, Stupid)


## es6 (es2015) 특징
#### let
* 다른 블록 내에 정의된 변수를 출력하려면 error 발생
* 실수로 다른 범위에 변수 액세스시 버그 발견, 잠재적 부작용을 미리 피할수 있다.

#### const
* 읽기 전용 변수로 동작, 단 다른 언어의 상수와 다르다
* 코드 내 다른곳에서 실수로 할당된 변수를 재할당 하지 않도록 하는데 유용

#### arrow function
* **화살표 함수 내부의 this 값은 부모 블록의 값과 같다**
* **lexical scope(어휘 범위)로 바인드 된다.**

#### class
* 단순히 구문상의 편의를 위한 것
* extends, super 키워드를 사용해서 상속 가능 
* 실제로는 prototype을 통해 속성과 함수를 상속

#### object literal
* 변수 및 함수를 객체의 멤버로 지정하고, 객체를 생성할때 동적인 멤버명을 정의 가능
* setter, getter

#### map, set
#### WeakMap, WeakSet
* WeakMap
  - 요소 전체를 반복 구문으로 탐색할 방법이 없고, 객체만을 키로 가질 수 있다.
  - 키로 사용된 객체에 대한 유일한 참조가 WeakMap 내에만 남아있을 경우 GC 가능
  - 예: 어플리케이션의 생명 주기 내에서 삭제되어야 할 객체와 관련된 메타 데이터 저장시 유용

#### template literal
* `${expression}` 사용 가능, `(역 따옴표)
* 단일 문자열을 여러 행에 걸쳐 쉽게 사용 가능

#### 나머지 기능
* default function parameters - (param = 1)
* rest parameters - (...args)
* spread operator - 배열, [...array]
* destructuring - { attr1, attr2 }
* new.target - 처음 본다
* Proxy
* Reflect
* Symbols - 처음 본다

## Reactor Pattern
#### blocking I/O vs non-blocking I/O vs asynchronous
세 가지 비교를 할 수 있는가? 확인할 수 없다면, 다음 링크를 꼭 읽어보라. (http://asfirstalways.tistory.com/348)
* non-blocking I/O : 즉시 그 시점에 가능한 data return
  - polling: 반환할 수 있는 데이터를 지속적으로 확인, busy-waiting
* blocking I/O : 전체 data return 할때까지 대기
* asynchronous: 값을 반환하지 않고, 해당 작업이 완료되면 통지받고, 그에 따른 작업 수행

#### 이벤트 디멀티플렉서 (이벤트 통지 인터페이스)
* 감시된 일련의 리소스들로부터 들어오는 I/O 이벤트를 수집하여 큐에 넣고, 처리할 수 있는 새 이벤트가 있을 때까지 차단
![event-demultiplexer](https://github.com/wearetuner/node-design-pattern/resources/images/node_event_demultiplexer.jpeg)
* event loop
  - 각 이벤트와 관련된 리소스는 읽기 작업을 위한 준비가 되어 있고, 차단되지 않은 상황 보증
  - 이벤트가 처리되고 나면, 다시 디멀티플렉서에서 처리 가능한 이벤트가 발생할때까지 차단

#### Reactor Pattern
* 관찰 대상 리소스에서 새 이벤트를 사용할 수 있을때까지 차단하여 I/O 처리 후, 각 이벤트를 핸들로러 전달함으로써 반응
* http://ozt88.tistory.com/25

#### libuv
* 서로 다른 OS 별로 논블록킹 지원 여부 다르다. 이벤트 디멀티플레서 추상화 필요 -> C 라이브러리로 생성
* 주요 플랫폼 호환 가능, 서로 다른 유형의 리소스들의 논블로킹 표준화 가능
