# 7️⃣ Ch7

### 7.1 레코드 캡슐화하기

대부분의 프로그래밍 언어는 데이터 레코드를 표현하는 구조를 제공한다. 레코드는 연관된 여러 데이터를 직관적인 방식으로 묶을 수 있어서 ⇒ 의미 있는 단위로 전달할 수 있게 해준다.

하지만, 단점은?

⇒ 계산해서 얻을 수 있는 값과 그렇지 않은 값을 명확히 구분해 저장해야 한다는 점!

* 가변 데이터를 저장하는 용도로는 레코드보단, 객체!
  * 각각의 메서드로 제공 가능
* 레코드 구조
  * 필드 이름을 노출하는 형태
  * 해시, 맵, 해시맵, 딕셔너리, 연관 배열 등의 이름으로 제공

\[절차]

1. 레코드를 담은 변수를 캡슐화한다.
2. 레코드를 감싼 단순한 클래스로 해당 변수의 내용을 교체한다. 이 클래스에 원본 레코드를 반환하는 접근자도 정의하고, 변수를 캡슐화하는 함수들이 이 접근자를 사용하도록 수정한다.
3. 테스트한다.
4. 원본 레코드 대신 새로 정의한 클래스 타입의 객체를 반환하는 함수들을 새로 만든다.
5. 레코드를 반환하는 예전 함수를 사용하는 코드를 4에서 만든 새 함수를 사용하도록 바꾼다. 필드에 접근할 때는 객체의 접근자를 사용한다. 적절한 접근자가 없다면 추가한다. 한 부분을 바꿀 때마다 테스트한다.
6. 클래스에서 원본 데이터를 반환하는 접근자와 원본 레코드를 반환하는 함수들을 제거한다.
7. 테스트한다.
8. 레코드의 필드도 데이터 구조인 중첩 구조라면 레코드 캡슐화하기와 컬렉션 캡슐화하기를 재귀적으로 적용한다.

**`예시1: 간단한 레코드 캡슐화하기`**

```jsx
const org = {name: '채민', country: 'korea'};
```

→ 레코드 구조의 상수 객체

1. 변수 캡슐화하기

```jsx
function getRawDataOfOrg() {
	return org;
}
```

```jsx
// 읽고 쓰는 경우

<div>getRawDataOfOrg().name</div>
getRawDataOfOrg().name = newName;
```

2. 레코드→클래스로 변경, 새 클래스의 인스턴스 반환하는 함수 생성

```jsx
class Org {
	constructor(data) {
		this._data = data;
	}
}

//
const org = new Org({...});
function getRawDataOfOrg() {return org._data;}
function getOrg() {return org;}
```

3. 레코드를 갱신하던 코드는 모두 세터를 사용하도록 고치기

get Name()

set name(aString) {this.\_data.name = aString;}

…

완성본

```jsx
class Org {
	constructor(data) {
		this._data = data.name;
		this._country = data.country;
	}
	get name() {return this._name;}
	set name(aString) {this._name = aString;}
	get country() {return this._country};
	set country(aCountryCode) {this._country = aCountryCode;}
}
```

**`예시2: 중첩된 레코드 캡슐화하기`**

* json 형태 뎁스가 높은 것들을 타겟

1. 변수 캡슐화부터 시작
2. 전체 데이터 구조 표현하는 클래스 정의, 이를 반환하는 함수 새로 생성

* 읽기 처리 방법
  *   세터 때와 같은 방법 적용

      ⇒ 읽는 코드를 모두 독립 함수로 추출한 다음 고객 데이터 클래스로 옮기는 것
  * 레코드 캡슐화를 재귀적으로 하는 것

### 7.2 컬렉션 캡슐화하기

* 변경자 메서드 만들기

\[절차]

1. 변수 캡슐화하기
2. 컬렉션에 원소를 추가/제거하는 함수 추가
3. 정적 검사 수행
4. 컬렉션을 참조하는 부분을 모두 찾는다. 컬렉션의 변경자를 호출하는 코드가 모두 앞에서 추가한 추가/제거 함수를 호출하도록 수정한다. 하나씩 수정할 때마다 테스트
5. 컬렉션 게터를 수정해서 원본 내용을 수정할 수 없는 읽기 전용 프락시나 복제본을 반환하게 한다.
6. 테스트

### 7.3 기본형을 객체로 바꾸기

```jsx
orders.filter(o => 'high' === o.priority || 'rush' === o.priority);
```

```jsx
orders.filter(o => o.priority.highterThan(new Priority('normal')));
```

### 7.4 임시 변수를 질의 함수로 바꾸기

```jsx
// before
const basePrice = this._quantity * this._itemPrice;
if (basePrice > 1000) return basePrice * 0.95;
else return basePrice * 0.98;
```

```jsx
// after
get basePrice() {this._quantity * this._itemPrice;}

if (this.basePrice > 1000) return this.basePrice * 0.95;
else return this.basePrice * 0.98;
```

* 클래스 내에 적용할 때 효과가 가장 크다

\[절차]

1. 변수 사용 전, 값이 확실히 결정되는지, 매번 계산 로직이 다른 결과를 내지 않는지 확인
2. 변수들을 최대한 읽기전용으로 바꾸기
   * `const`로 바꾸기
3. test
4. 변수 대입문을 함수로 추출
5. test
6. 변수 인라인하기 (임시 변수 제거)

### 7.5 클래스 추출하기

```jsx
// before
class Person {
  get officeAreaCode() {
    return this._officeAreaCode;
  }
  get officeNumber() {
    return this._officeNumber;
  }
}
```

```jsx
// after
class Person {
  get officeAreaCode() {
    return this._telephoneNumber.areaCode;
  }
  get officeNumber() {
    return this._telephoneNumber.number;
  }
}

class TelephoneNumber {
  get areaCode {return this._areaCode;}
  get number {return this._number;}
}
```

\[절차]

1. 클래스 역할을 분리할 방법 결정
2. 분리될 역할을 담당할 클래스 만들기
3. 원래 클래스의 생성자에서 새 클래스 인스턴스 생성 + 필드에 저장
4. 필드 옮기기 (from 원래 클래스 to 새 클래스) + test
5. 함수 옮기기 (from 원래 클래스 to 새 클래스) + test
   * 저수준 메서드부터 ('호출 하기'보다는 '호출 당하는' 경우가 많은 메서드)
6. 양쪽 클래스에서 불필요한 메서드 제거
7. 클래스명 적절히 변경
8. 새 클래스를 외부로 노출할지 결정

### 7.6 클래스 인라인하기

```jsx
// before
class Person {
  get officeAreaCode() {
    return this._telephoneNumber.areaCode;
  }
  get officeNumber() {
    return this._telephoneNumber.number;
  }
}

class TelephoneNumber {
  get areaCode() {
    return this._areaCode;
  }

  get number() {
    return this._number;
  }
}
```

```jsx
// after
class Person {
  get officeAreaCode() {
    return this._officeAreaCode;
  }
  get officeNumber() {
    return this._officeNumber;
  }
}
```

* 더이상 역할이 없어져 그대로 두면 안되는 클래스들은 인라인하기
  * 특정 클래스에 남은 역할이 거의 없을 때
  * 두 클래스의 기능을 지금과 다르게 배분하고 싶을 때 ⇒ 하나로 합친 다음, 다른 방식으로 추출

\[절차]

1. 소스 클래스의 각 public 메서드에 대응하는 메스드들을 타깃 클래스에 생성
   * 메서드들은 단순히 원래 클래스로 위임하는 역할
2. 소스 클래스 메서드를 사용하는 부분을, 타깃 클래스 메서드로 바꾸기
3. 소스 클래서의 메서드, 필드를 모두 타깃 클래스로 이동시키기
4. 소스 클래스 삭제

### 7.7 위임 숨기기

```jsx
// before
manager = aPerson.department.manager;
```

```jsx
// after
manager = aPerson.manager;

class Person {
    get manager() {
        return this.department.manager;
    }
}
```

* 위임 메서드
  * 클라이언트가 객체의 메서드를 호출할 때엔 해당 객체에 대한 정보를 알아야 함
  * 의존성을 없애려면, 위임 메서드를 만들어 위임 객체의 존재를 숨길 수 있다.
  * 위임 객체가 수정되어도 클라이언트는 아무 영향 받지 X

\[절차]

1. 위임 객체의 각 메서드에 해당하는 서버에 생성
2. 클라이언트가 위임 객체가 아닌 서버를 호출하도록 수정
3. 서버로부터 위임 객체를 얻는 접근자를 제거
4. 테스트

### 7.8 중개자 제거하기

```jsx
// before
manager = aPerson.manager;

class Person {
    get manager() {
        return this.department.manager;
    }
}
```

```jsx
// after
manager = aPerson.department.manager;
```

* 위임 숨기기의 반대!
*   위임 숨기기를 계속 하게 되면 한 클래스가 그저 ‘중개자’의 역할로 전락할 위험

    ⇒ 이런 경우에는 차라리 클라이언트가 위임 객체를 직접 호출하는 게 낫다.
* 적합하게 섞어 쓰기..

\[절차]

1. 위임 객체를 얻는 게터 생성
2. 위임 객체를 호출하는 클라이언트가 이 게터를 거치도록 수정
3. 위임 메서드 삭제

### 7.9 알고리즘 교체하기

```jsx
// before
function foundPerson(people) {
    for (let i = 0; i < people.length; i++) {
        if (people[i] === "Don") return "Don";
        if (people[i] === "John") return "John";
        if (people[i] === "Kent") return "Kent";
    }
    return "";
}
```

```jsx
// after
function foundPerson(people) {
    const candidates = ["Don", "John", "Kent"];
    return people.find(p => candidates.includes(p)) || "";
}
```

* 요건 뭐.. 코테 느낌쓰
* 더 쉽고 간결하게 작성하기 위해 전체를 걷어내고 바꾸기

\[절차]

1. 교체할 코드를 함수 하나에 모으기
2. 이 함수의 동작을 검증하는 테스트 마련
3. 대체할 알고리즘 준비
4. 정적 검사 수행
5. 기존/새 알고리즘 결과 비교 테스트
