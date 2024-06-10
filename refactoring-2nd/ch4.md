# 4️⃣ Ch4

### 4.1 자가 테스트 코드의 가치

> **`모든 테스트를 완전히 자동화하고 그 결과까지 스스로 검사하게 만들자.`**

> **`테스트 스위트는 강력한 버그 검출 도구로, 버그를 찾는 데 걸리는 시간을 대폭 줄여준다.`**

* JUnit(제이유닛)..
* **테스트를 작성하기 가장 좋은 시점**
  * 프로그래밍을 시작하기 전!
  *   ex. 기능을 추가해야 할 때 테스트부터 작성

      ⇒ 테스트를 모두 통과한 시점이 바로 코드를 완성한 시점
* **TDD(Test-Driven Development) 테스트 주도 개발**

처음에는 통과하지 못할 테스트를 작성하고, 이 테스트를 통과하게끔 코드를 작성하고, 결과 코드를 최대한 깔끔하게 리팩터링하는 과정을 짧은 주기로 반복한다.

⇒ 테스트-코딩-리팩터링 과정

### 4.2 테스트할 샘플 코드

테스트 코드를 짤 때는, 코드를 항상 `성격`에 따라 분리하는 것이 좋다.

* 생산자 - Producer
* 지역 전체 - Province
  * 생성자는 JSON 문서로부터 만들어진 JS 객체를 인수로 받음

### 4.3 첫 번째 테스트

* Mocha
  * 테스트 코드를 블록 단위로 나눠서 각 블록에 테스트 스위트를 담는 구조
  * 테스트는 it 블록에 담긴다.
  * 방법
    *
      1. 픽스처 설정
    *
      2. 검증

> **`실패해야할 상황에서는 반드시 실패하게 만들자.`**

> **`자주 테스트하라. 작성 중인 코드는 최소한 몇 분 간격으로 테스트하고, 적어도 하루에 한 번은 전체 테스트를 돌려보자.`**

### 4.4 테스트 추가하기

* 테스트는 위험요인을 중심으로 작성해야 한다.

> **`완벽하게 만드느라 테스트를 수행하지 못하느니, 불완전한 테스트라도 작성해 실행하는 게 낫다.`**