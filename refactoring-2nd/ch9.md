# Ch9

### 9.1 변수 쪼개기

```jsx
// before
let temp = 2 * (height + width);
console.log(temp);
temp = height * width;
console.log(temp);
```

```jsx
// after
const perimeter = 2 * (height + width);
console.log(perimeter);
const area = height * width;
console.log(area);
```

#### \[배경]

역할이 둘 이상인 변수가 있다면 쪼개야 한다.

자바스크립트의 매개변수는 값에 의한 호출 (call-by-value) 방식으로 전달되므로 inputValue를 수정해도 호출자에 영향을 주지 않는다.

#### \[절차]

1. 변수를 선언한 곳과 값을 처음 대입하는 곳에서 변수 이름을 바꾼다. 가능한 immutable로 선언하기
2. 이 변수에 두 번째로 값을 대입하는 곳 앞까지의 모든 참조를 새로운 변수 이름으로 바꾼다.
3. 두 번째 대입 시 변수를 원래 이름으로 다시 선언한다.
4. 테스트
5. 반복

### 9.2 필드 이름 바꾸기

```jsx
// before
class Organization {
    get name() { ... }
}
```

```jsx
// after
class Organization {
    get title() { ... }
}
```

#### \[배경]

* 데이터 구조는 무슨 일이 벌어지는지를 이해하는 key이다.
* 데이터 구조는 프로그램 이해에 큰 역할을 하기 때문에 반드시 깔끔하게 관리해야 한다.
* 클래스도 마찬가지. getter, setter 이름 바꾸기도 중요

#### \[절차]

1. 레코드 유효 범위가 제한적이라면, 필드에 접근하는 모든 코드를 수정한 후 테스트한다.
2. 레코드가 캡슐화되지 않았다면, 먼저 레코드 캡슐화를 진행한다.
3. 캡슐화된 객체의 private 필드명을 변경하고 내부 메서드를 수정한다.
4. 테스트
5. 생성자 매개변수 중 필드와 이름이 겹치는 게 있으면 ‘함수 선언 바꾸기’로 변경한다.
6. 접근자 이름도 수정한다.

### 9.3 파생 변수를 질의 함수로 바꾸기

```jsx
// before
get discountedTotal() {
    return this._discountedTotal;
}

set discount(aNumber) {
    const old = this._discount;
    this._discount = aNumber;
    this._discountedTotal += old - aNumber;
}
```

```jsx
// after
get discountedTotal() {
    return this._baseTotal - this._discount;
}

set discount(aNumber) {
    this._discount = aNumber;
}
```

#### \[배경]

* 골칫거리 가변 데이터의 유효 범위를 가능한 좁히는 게 좋다.
  * 그러기 위해서 값을 쉽게 계산해낼 수 있는 변수를 모두 제거할 수 있다.
* 예외 - 변형 연산(transformation operation)이라면 비록 계산 코드로 대체할 수 있더라도 그대로 두는 것도 좋다.
  * 새로운 데이터 구조를 생성하는 경우
    * 데이터 구조를 감싸며 그 데이터에 기초하여 계산한 결과를 속성으로 제공하는 객체
    * 데이터 구조를 받아 다른 데이터 구조로 변환해 반환하는 함수

#### \[절차]

* 변수 값이 갱신되는 지점을 모두 찾는다. 필요 시엔 ‘변수 쪼개기’를 활용해 각 갱신 지점에서 변수를 분리한다.
* 해당 변수의 값을 계산해주는 함수를 만든다.
* 해당 변수가 사용되는 모든 곳에 어서션을 추가하여 함수의 계산 결과가 변수의 값과 같은지 확인한다.
* 테스트한다.
* 변수를 읽는 코드를 모두 함수 호출로 대체한다.
* 테스트한다.
* 변수를 선언하고 갱신하는 코드를 ‘죽은 코드 제거하기’로 없앤다.

### 9.4 참조를 값으로 바꾸기

```jsx
// before
class Product {
    applyDiscount(arg) {
        this._price.amount -= arg;
    }
}
```

```jsx
// after
class Product {
    applyDiscount(arg) {
        this._price = new Money(this._price.amount - arg, this._price.currency);
}
```

#### \[배경]

필드를 값으로 다루면 내부 객체의 클래스를 수정해 Value Object로 만들 수 있다. 값 객체는 불변이기 때문에 다루기 더 쉽다.

* 참조: 내부 객체는 그대로 두고, 객체의 속성만 갱신
* 값: 새로운 속성을 담은 객체로 기존 객체를 통째로 대체

⇒ 값 객체는 불변이기 때문에 값을 복제해 이곳저곳 사용하더라도 서로간의 참조를 관리하지 않아도 된다.

#### \[절차]

1. 후보 클래스가 불변인지, 혹은 불변이 될 수 있는지 확인한다.
2. 각각의 세터를 하나씩 제거한다.
3. 이 값 객체의 필드를 사용하는 동치성 비교 메서드를 만든다.

### 9.5 값을 참조로 바꾸기

```jsx
// before
let customer = new Customer(customerData);
```

```jsx
// after
let customer = customerRepository.get(customerData.id);
```

#### \[배경]

* 데이터를 여러 곳에서 참조하고, 갱신할 일이 많다면 데이터의 일관성을 위해 참조로 바꾸는 방법을 사용하기도 한다.
* 같은 데이터를 물리적으로 복제해 사용하는 경우 가장 크게 문제되는 상황은 그 데이터를 갱신할 때이다.
  * 이런 상황이라면 복제된 데이터를 모두 참조로 바꿔주는 게 좋다.
  * 이러한 경우 보통 객체들을 한곳에 모아놓고 클라이언트의 접근을 관리해주는 **일종의 저장소**가 필요해진다. ⇒ 각 엔티티를 표현하는 객체를 한 번만 만들고, 사용처에서 저장소로부터 얻어쓰는 방식

#### \[절차]

1. 같은 부류에 속하는 객체들을 보관할 저장소를 만든다. 이미 있다면 생략.
2. 생성자에서 이 부류의 객체들 중 특정 객체를 정확히 찾아내는 방법이 있는지 확인한다.
3. 호스트 객체의 생성자를 수정해 필요한 객체를 찾을 수 있게 한다.

### 9.6 매직 리터럴 바꾸기

```jsx
// before
function potentialEnergy(mass, height) {
    return mass * 9.81 * height;
}
```

```jsx
// after
const STANDARD_GRAVITY = 9.81;
function potentialEnergy(mass, height) {
    return mass * STANDARD_GRAVITY * height;
}
```

#### \[배경]

* 매직 리터럴 == 소스 코드 여러 곳에 등장하는 일반적인 리터럴 값
* 코드를 읽는 사람이 값의 의미를 모른다면 숫자 자체로는 의미를 명확히 알려주지 못하므로 매직 리터럴이라고 할 수 있다.

**❓어떻게 지을까**

* 상수, 코드 자체가 뜻을 분명하게 드러내도록 이름 짓기
* 과용 금지
  * ex. const ONE = 1 (x)
* 함수 호출로 바꾸는 방법도 있다.
  * ex. aValue === MALE\_GENDER → isMale(aValue)

#### \[절차]

1. 상수 선언 후 매직 리터럴 대입
2. 해당 리터럴이 사용되는 곳을 모두 찾기
3. 찾은 곳 모두에서 리터럴이 새 상수와 똑같은 의미로 쓰였는지 확인 후 대체

#### → 테스트

* 상수 값을 바꾸고, 관련 테스트 모두가 바뀐 값에 해당하는 결과를 내는지 확인해보기
