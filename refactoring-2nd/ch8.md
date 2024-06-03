# Ch8

### 8.1 함수 옮기기

```jsx
// before
class Account {
	get overdraftCharge() {}
}
```

```jsx
// after
class AccountType {
	get overdraftCharge() {}
}
```

* `모듈성`
  * 프로그램을 수정할 때, 관련된 일부만 이해해도 수정할 수 있도록 하는 능력 (== 분리가 잘 되어있다는 의미)
  * 연관된 요소는 함께 묶고, 요소 간 연결 관계를 쉽게 찾고 이해할 수 있도록 해야 함
* 모든 함수는 어떤 ‘컨텍스트’ 안에 존재
  * 클래스
  * 함수 중첩
* 여러 개의 함수를 함께 옮기는 경우, 호출하는 함수보다 호출되는 함수를 먼저 옮기는 게 낫다.

\[절차]

1. 선택한 함수가 현재 컨택스트에서 사용 중인 모든 프로그램 요소를 살펴본다. 이 요소들 중에도 함께 옮겨야 할 게 있는지 고민해보기
2. 선택한 함수가 다형 메서드인지 확인한다.
   * 객체 지향 언어의 다형성(polymorphism)을 구현하는 메서드인지 확인한다는 의미
   * 슈퍼클래스나 서브클래스에도 선언된 메서드라면 전부 같이 옮겨 줘야 하니까
3. 선택한 함수를 타깃 컨텍스트로 복사한다(이 때 원래의 함수를 소스 함수라 하고 복사해서 만든 새로운 함수를 타깃 함수라 한다). 타깃 함수가 새로운 터전에 잘 자리잡도록 다듬는다.
4. 정적 분석을 수행한다.
5. 소스 컨텍스트에서 타깃 함수를 참조할 방법을 찾아 반영한다.
6. 소스 함수를 타깃 함수의 위임 함수가 되도록 수정한다.
7. 테스트한다.
8. 소스 함수를 인라인(6.2절)할지 고민해본다.

### 8.2 필드 옮기기

```jsx
// before
class Customer {
    get plan() {return this._plan;}
    get discountRate() {return this._discountRate;}
}
```

```jsx
// after
class Customer {
    get plan() {return this._plan;}
    get discountRate() {return this.plan.discountRate;}
}
```

**`*“프로그램의 진짜 힘은 ‘데이터 구조’에서 나온다.”*`**

> 필드 옮기기 리팩터링은 대체로 더 큰 변경의 일환으로 수행된다. 예컨대 필드 하나를 잘 옮기면, 그 필드를 사용하던 많은 코드가 원래 위치보다 옮겨진 위치에서 사용하는 게 더 수월할 수 있다. 그렇다면 리팩터링을 마저 진행하여 호출 코드들까지 모두 변경한다.

* 문제에 적합한 데이터 구조를 활용하면, 동작 코드는 직관적으로 짜여짐
  * 도메인 주도 설계 등의 기술로 능력 키울 수 있다.
  * 데이터 구조가 부적절하다고 깨달으면 바로 수정하기
* 함수에 항상 같이 건네지는 데이터 무리는 한 레코드에 담기
  * 상호 관계가 명확히 드러나도록
  * 여러 구조체에 정의된 같은 필드들을 갱신하는 것 == 한 번만 갱신하는 곳으로 옮기라는 시그널

\[절차]

1. 소스필드 캡슐화
2. 테스트
3. 타깃 객체에 필드, 접근자, 메서드 생성
4. 정적 검사
5. 소스 객체에서 타깃객체를 참조할 수 있는지 확인
6. 접근자들이 타깃필드를 사용하도록 수정
7. 테스트
8. 소스필드 제거
9. 테스트

### 8.3 문장을 함수로 옮기기

* “중복 제거”는 코드를 건강하게 관리하는 가장 효과적인 방법 중 하나
  * 똑같은 코드가 앞뒤로 실행된다면.. 반복되는 부분을 합치기!
  * 문장들을 함수로 옮기려면, 그 문장들이 피호출 함수의 일부라는 확신이 있어야 함

```jsx
// before
result.push(`<p>제목: ${person.photo.title}</p>`);
result.concat(photoData(person.photo));

function photoData(aPhoto) {
    return [
        `<p>위치: ${aPhoto.location}</p>`,
        `<p>날짜: ${aPhoto.date.toDateString()}</p>`,
    ];
}
```

```jsx
// after
result.concat(photoData(person.photo));

function photoData(aPhoto) {
    return [
        `<p>제목: ${aPhoto.title}</p>`,
        `<p>위치: ${aPhoto.location}</p>`,
        `<p>날짜: ${aPhoto.date.toDateString()}</p>`,
    ];
}
```

\[절차]

1. 문장 슬라이드
   * 반복코드가 함수 호출부와 멀리 있다면 근처로 옮기기
2. 타깃함수를 호출하는 곳이 한 곳뿐이라면, 소스 위치에서 피호출 함수로 이동시키기
   * 이 경우에 해당하면 이제 끝
3. 타깃함수를 호출하는 곳이 둘 이상이라면, 호출자 중 하나에서 '타깃함수 호출부와, 그 함수를 옮기려는 문장들을 함께' 다른 함수로 추출
4. 다른 호출자 모두 3에서 추출한 함수를 사용하도록 수정
5. 원래 함수를 새 함수 안으로 인라인한 후, 원래 함수는 제거

* 단순히 원래 있던 함수 안으로 코드를 옮기는 게 아니라, 원래 함수와 함수 안으로 옮길 코드를 함께 호출하는 새로운 함수를 만들고, 각각의 호출이 새 함수로 바꿔 가며 테스트한 다음,, 그제서야 문제가 없으면 원래 함수의 내용을 새 함수 안으로 옮긴다.

**기승전테스트!**

### 8.4 문장을 호출한 곳으로 옮기기

```jsx
// before
emitPhotoData(outStream, person.photo);

function emitPhotoData(outStream, photo) {
    outStream.write(`<p>제목: ${photo.title}</p>\\n`);
    outStream.write(`<p>위치: ${photo.location}</p>\\n`);
}
```

```jsx
// after
emitPhotoData(outStream, person.photo);
outStream.write(`<p>위치: ${person.photo.location}</p>\\n`); // 여기서 쓰기

function emitPhotoData(outStream, photo) {
    outStream.write(`<p>제목: ${photo.title}</p>\\n`);
}
```

* 코드 작성 시 코드 간 순서 및 위치도 중요..
* 기능 범위가 달라지면 추상화의 경계도 움직인다. 초기엔 응집도 높고 한 가지 일만 수행하던 함수가 어느새 둘 이상의 다른 일을 수행하게 바뀔 수 있다는 뜻
  * 달라진 동작을 호출자로 옮기기
  * 필요할 때마다 ‘독립적’으로 수정할 수 있도록 하기
* ‘이동하지 않길 원하는 문장을 추출’하는 상황에서, 대상 함수가 다형 메서드라면 모든 구현에서 남길 부분을 메서드로 추출한 다음 슈퍼클래스의 메서드만 남긴다. 이때 남길 부분은 모든 구현에서 동일한 부분이어야 한다.

\[절차]

1. 단순한 상황이면, 피호출 함수 일부를 호출자로 복사해 넣기 ⇒ 테스트과 통과된다면 여기서 마무리
2. 복잡한 상황이면, 이동하지 않길 원하는 문장들을 다른 이름의 함수로 추출하기
3. 원래 함수 인라인
4. 추출된 함수 이름 변경

### 8.5 인라인 코드를 함수 호출로 바꾸기

```jsx
// before
let appliesToMass = false;
for (const s of states) {
    if (s == "MA") appliesToMass = true;
}
```

```jsx
// after
appliesToMass = states.includes("MA");
```

* 요것도 약간 코테느낌쓰
* 인라인 코드를 대체할 함수가 존재하다면 대체하기
  * **함수 이름을 ‘코드의 동작 방식’이 잘 드러나게 정하고 함수 사용하기**
  * 라이브러리 적극 활용

\[절차]

1. 인라인 코드를 함수 호출로 대체
2. 테스트!

### 8.6 문장 슬라이드하기

```jsx
// before
const pricingPlan = retrievePricingPlan();
const order = retrieveOrder();
let charge;
const chargePerUnit = pricingPlan.unit;
```

```jsx
// after
const pricingPlan = retrievePricingPlan();
const chargePerUnit = pricingPlan.unit;
const order = retrieveOrder();
let charge;
```

말 그대로! 코드 블록을 위아래로 옮기는 것!

\*한 문장만 옮길 때는 ‘문장 교환하기’라고 부름

공감되는 부분..!

> 관련 있는 코드들을 명확히 구분되는 함수로 추출하는 게 그저 문장들을 한데로 모으는 것보다 나은 분리법이다. 하지만 코드들이 모여 있지 않다면 함수 추출은 애초에 수행할 수조차 없다. 관련된 코드들이 가까이 모여 있다면 이해하기가 더 쉽다.

* 변수 선언
  * 변수를 처음 사용할 때 선언
* 함수 추출하기 준비단계로 많이 활용됨
* 조건문에서는 각 조건에서 중복되는 코드를 밖으로 빼기도 한다.

**🙂명령-질의 분리 원칙 (command-query separation principle)**

> **질의형 함수를 작성할 때는 요청한 값을 리턴하기만 하고, 데이터에 상태를 바꾸는 일은 하지 않는다. 반대로 데이터의 상태를 변경하는 함수는 값을 리턴하는 일도 하지 말아야 한다.** 코드를 최대한 명확하게 작성하려면 함수에서 값을 리턴하는 작업과 데이터의 상태를 변경하는 작업을 한 함수에서 동시에 처리해서는 안 된다.

* 함수 호출시 side effect로 예상치 못한 결과가 나오는 일을 방지하는 데 기초가 되는 원칙
* 함수는 성격에 따라 크게 두 가지로 분류
  * 명령(command): 어떤 동작을 수행
  * 질의(query): 답을 구함

\[절차]

1. 코드 조각을 이동할 목표 위치 찾기
2. 원래 위치에서 잘라내 목표 위치에 붙여넣기
3. 테스트

### 8.7 반복문 쪼개기

```jsx
// before
let averageAge = 0;
let totalSalary = 0;
for (const p of people) {
    averageAge += p.age;
    totalSalary += p.salary;
}
averageAge = averageAge / people.length; 
```

```jsx
// after
let totalSalary = 0;
for (const p of people) {
    totalSalary += p.salary;
}

let averageAge = 0;
for (const p of people) {
    averageAge += p.age;
}

averageAge = averageAge / people.length; 
```

종종 하나의 반복문 내에서 두 가지 일을 수행하는 모습을 볼 수 있다. 두 일을 한번에 처리할 수 있기 때문에 작성하는 경우가 있다.. 완전 나잖아?

⇒ 반복문을 쪼개자!

* 각각의 반복문으로 수정하기
  * **`리팩터링 ≠ 최적화`** 라는 사실 명심..
* 서로 다른 일들이 한 함수에서 이뤄지고 있다는 신호일 수도 있다.

\[절차]

1. 반복문을 복제해 두 개로 만들기
2. 중복되어 생기는 side effect를 파악해 제거
3. 테스트
4. 각 반복문을 함수로 추출할지 고민해보기

### 8.8 반복문을 파이프라인으로 바꾸기

```jsx
// before
const names = [];
for (const i of input) {
    if (i.job === "programmer") {
        names.push(i.name);
    }
}
```

```jsx
// after
const names = input.filter(i => i.job === 'programmer').map(i => i.name);
```

코테느낌쓰

> 논리를 파이프라인으로 표현하면 이해하기 훨씬 쉬워진다. 객체가 파이프라인을 따라 흐르며 어떻게 처리되는지 읽을 수 있기 때문이다.

**⇒ 즉, filter, map, reduce와 같은 컬렉션 파이프라인을 쓰자는 내용**

* 컬렉션 파이프라인을 이용하면 처리 과정을 일련의 연산으로 표현할 수 있다.

\[절차]

1. 반복문에서 사용하는 컬렉션을 가리키는 변수를 하나 만든다.
2. 반복문의 첫 줄부터 시작해서, 각각의 단위 행위를 적절한 컬렉션 파이프라인 연산으로 대체한다. 이때 컬렉션 파이프라인 연산은 1에서 만든 반복문 컬렉션 변수에서 시작하여, 이전 연산의 결과를 기초로 연쇄적으로 수행된다. 하나를 대체할 때마다 테스트한다.
3. 반복문의 모든 동작을 대체했다면 반복문 자체를 지운다.

### 8.9 죽은 코드 제거하기

말 그대로!

⇒ 사용하지 않는 코드가 있다면, 소프트웨어 동작을 이해하는 데 커다란 걸림돌이 될 수 있다.

⇒ 제거하자!

* 더 이상 사용되지 않는 코드는 지우기
* 주석을 달거나, 미래를 걱정하며 남겨둘 필요가 없다. (버전관리 시스템이 있으니까)
