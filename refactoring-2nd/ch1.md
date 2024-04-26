---
description: ch1
---

# Ch1

### 1.2 예시 프로그램을 본 소감

* _**프로그램이 새로운 기능을 추가하기에 편한 구조가 아니라면, 먼저 기능을 추가하기 쉬운 형태로 리팩터링하고 나서 원하는 기능을 추가한다.**_
* 리팩터링이 필요한 이유 : “변경”할 일이 반드시 있기 때문

### 1.3 리팩터링의 첫 단계

* 테스트 코드 마련
  * 성공/실패를 스스로 판단하는 자가진단 테스트로 만들기
    * ex. 초록불과 빨간불
* _**리팩터링하기 전에 제대로 된 테스트부터 마련한다. 테스트는 반드시 자가진단하도록 만든다.**_

### 1.4 statement() 함수 쪼개기

* 코드 조각을 별도 함수로 추출하는 방식 → `**“함수 추출하기”**`
  * ex. amoountFor(aPerformance)
* 아무리 간단한 수정이라도 리팩터링 후에는 항상 테스트하는 습관 들이기!
* 피드백 주기를 짧게 가져가는 습관 들이기
* _**리팩터링은 프로그램 수정을 작은 단계로 나눠 진행한다. 그래서 중간에 실수하더라도 버그를 쉽게 찾을 수 있다.**_
* 함수 추출하기 이후, 변수명 검토
  * ex. thisAmount → result로 변경 (더 명확하게)
  * ex. perf → aPerformance
    *   a를 붙인 이유는?

        ⇒ 자바스크립트와 같은 동적 타입 언어를 사용할 때는 타입이 드러나게 작성하면 도움된다.

        ⇒ 매개변수 이름에 접두어로 타입 이름을 적는 편, but 매개변수의 역할이 뚜렷하지 않을 때는 부정 관사(a/an)을 붙인다.
* _**컴퓨터가 이해하는 코드는 바보도 작성할 수 있다. 사람이 이해하도록 작성하는 프로그래머가 진정한 실력자다.**_

**\[play 변수 제거하기]**

* **`“임시 변수를 질의 함수로 바꾸기"`**
  *   before

      ```jsx
      const play = plays[perf.playID];
      ```
  *   after - 대입문(=)의 우변을 함수로 추출

      ```jsx
      function playFor(aPerformance) {
      	return plays[aPerformance.playID];
      }

      ...
      const play = playFor(perf); // 우변을 함수로 추출
      let thisAmount = amountFor(perf, play);
      ```
*   컴파일-테스트-커밋 후 **`“변수 인라인하기”`** 적용

    ```jsx
    ~~const play = playFor(perf);~~ // 인라인된 변수는 제거
    let thisAmount = amountFor(perf, playFor(perf));
    ```
* 다시 컴파일-테스트-커밋
* 변수를 인라인한 덕분에 amountFor()에 **`“함수 선언 바꾸기”`** 적용
  *   statement() 안에서도 play를 playFor() 호출로 변경

      ```jsx
      function amountFor(aPerformance, play) {
      	...
      	switch (playFor(aPerformance).type) { // play를 playFor() 호출로 변경
      		...
      	}
      	...
      }
      ```
*   다시 컴파일-테스트-커밋 후 play 매개변수 삭제

    ```jsx
    function amountFor(aPerformance, ~~play~~) { // play는 더이상 필요없어짐
    	...
    	switch (playFor(aPerformance).type) { // play를 playFor() 호출로 변경
    		...
    	}
    	...
    }
    ```
* 다시 한번 컴파일-테스트-커밋

**❓지역 변수를 제거해서 얻는 가장 큰 장점**

⇒ 추출 작업이 훨씬 쉬워진다는 것

⇒ 유효범위를 신경 써야 할 대상이 줄어들기 때문

**\[적립 포인트 계산 코드 추출하기]**

*   volumeCreditsFor 함수 새로 추출

    ```jsx
    volumeCredits += volumeCreditsFor(perf); // 추출한 함수를 이용해 값을 누적
    ```
* 똑같이 계속해서 컴파일-테스트-커밋

**\[format 변수 제거하기]**

임시 변수는 자신이 속한 루틴에서만 의미가 있어서 루틴이 길고 복잡해지기 쉽기에 나중에 문제를 일으킬 위험이 있다.

```jsx
const format = new Intl.NumberFormat("en-US", { ... }).format;
```

* 여기서 format은 임시 변수에 함수를 대입한 형태
*   새로운 함수로 추출

    ```jsx
    function format(aNumber) {
    	return new Intl.NumberFormat("en-US", { ... }).format(aNumber);
    }
    ```
*   아직 부족.. **`“함수 선언 바꾸기”`** 적용

    ```jsx
    ... usd(amountFor(perf))... // usd로 함수 이름 변경
    ...

    function usd(aNumber) {
    	return new Intl.NumberFormat("en-US", { ... }).format(aNumber/100); // 단위 변환 로직도 이 함수 안으로 이동
    }
    ```

    * 긴 함수를 작게 쪼개는 리팩터링을 할 때 중요한 점!!!!!! 이름 잘 짓기..
      * tip : 흔히 코드를 두 번 이상 읽고 나서야 가장 적합한 이름으로 바꿀 수 있음

**\[volumeCredits 변수 제어하기]**

* **`“반복문 쪼개기”`**
  * 하나의 for문을 두 개로 분리 (값 누적 로직을 별도 for문으로 분리)
* **`“문장 슬라이드하기”`**
* \*\*“임시 변수를 질의 함수로 바꾸기”\*\*가 수월해짐

저자의 말

> “리팩터링으로 인한 성능 문제에 대한 내 조언은 ‘특별한 경우가 아니라면 일단 무시하라’는 것이다. 리팩터링 때문에 성능이 떨어진다면, 하던 리팩터링을 마무리하고 나서 성능을 개선하자”

음.. 그렇다고 한다.

**단계 정리**

1. `반복문 쪼개기`로 변수 값을 누적시키는 부분을 분리한다.
2. `문장 슬라이드하기`로 변수 초기화 문장을 변수 값 누적 코드 바로 앞으로 옮긴다.
3. `함수 추출하기`로 적립 포인트 계산 부분을 별도 함수로 추출한다.
4. `변수 인라인하기`로 volumeCredits 변수를 제거한다.

### 1.5 중간 점검: 난무하는 중첩 함수

* 앞에서 statement() 쪼개기를 수행한 결과, 최상위의 statement() 함수는 단 일곱 줄!, 출력할 문장을 생성하는 역할만 한다.
* 각 계산 과정은 물론 전체 흐름도 이해하기가 훨씬 쉬워졌다.

### 1.6 계산 단계와 포맷팅 단계 분리하기

### 1.7 중간 점검: 두 파일(과 두 단계)로 분리됨

### 1.8 다형성을 활용해 계산 코드 재구성하기

### 1.9 상태 점검: 다형성을 활용하여 데이터 생성하기

### 1.10 마치며
