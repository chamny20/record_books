# ITEM1

### ITEM1 타입스크립트와 자바스크립트의 관계 이해하기

* 타입스크립트
  * 자바스크립트의 상위집합
  * 자바스크립트는 .js, .jsx 사용, 타입스크립트는 .ts, .tsx 사용
  * 확장자를 서로 변경해도 달라지는 것은 없기 때문에 js 코드를 ts로 마이그레이션하는 데 엄청난 이점이 된다.
* 모든 자바스크립트 프로그램이 타입스크립트라는 명제는 참이지만, 그 반대는 성립하지 않는다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/04de4478-f1ad-4a07-9e04-e054fd007465/fb8bde1f-07ce-4a21-b82d-161c5ec08a4b/Untitled.png)

* 예제

```jsx
let city = 'new york city';
console.log(city.toUppercase());

// 오류 발생
// TypeError: city.toUppercase is not a function

// 'toUppercase' 속성이 'string' 형식에 없습니다. ...
```

⇒ 앞의 코드에는 타입 구문이 없지만, 타입스크립트의 타입 체커는 문제점을 찾아낸다.

**⇒ city 변수가 문자열이라는 것을 알려 주지 않아도 ts는 초기값으로부터 타입을 추론한다. (타입추론)**

*   타입 시스템의 목표 → 런타임에 오류를 발생시킬 코드를 미리 찾아내는 것

    ⇒ ts가 ‘정적’ 타입 시스템이라는 것은 바로 이런 특징을 말하는 것
* 예제 (js program)

```jsx
const states = [
	{name: 'Alabama', capital: 'Montgomery'},
	{name: 'Alaska', capital: 'Juneau'},
	{name: 'Arizona', capital: 'Phoenix'},
	//...
];
for (const state of states) {
	console.log(state.capitol); // 오타 아님
}

// 실행결과
/*
undefined
undefined
undefined
*/
```

⇒ state.capitol은 의도한 코드가 아니다. 이런 경우에 ts 타입 체커는 추가적인 타입 구문 없이도 오류를 찾아낸다.

`⇒ ‘capitol’ 속성이 … 형식에 없습니다. ‘capital’을(를) 사용하시겠습니까?`

⇒ ts는 타입 구문 없이도 오류를 잡을 수 있지만, 타입 구문을 추가한다면 훨씬 더 많은 오류를 찾아낼 수 있다.

⇒ 하지만, 어떤 게 오타인지는 판단하지 못한다. (당연한 사실)

따라서 다음과 같이 인터페이스를 정의할 수 있다. ⇒ **명시적으로 states 정의**

```jsx
interface State {
	name: string;
	capital: string;
} 

...
```

⇒ 이렇게 State라는 인터페이스를 정의하면 오류가 어디에서 발생했는지 찾을 수 있고, 솔루션 또한 정확하게 제시된다.

그렇다면 다시 벤 다이어그램을 정의해볼 수 있다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/04de4478-f1ad-4a07-9e04-e054fd007465/cfe05a57-9130-4991-ab22-b121e4377999/Untitled.png)

**`⇒ 모든 자바스크립트는 타입스크립트이지만, 일부 자바스크립트(or 타입스크립트)만이 타입 체크를 통과한다.`**

* 타입 체커의 문제점 표시 방식

```jsx
const a = null + 7;
// JS -> 자바스크립트에서는 a값이 7이 됨
// TS -> '+' 연산자를 ... 형식에 적용할 수 없습니다.
const b = [] + 12;
// JS -> 자바스크립트에서는 b값이 '12'가 됨
// TS -> '+' 연산자를 ... 형식에 적용할 수 없습니다.
alert('Hello', 'ABC');
// JS -> "Hello" 경고를 표시함
// TS -> 0-1개의 인수가 필요한데 2개를 가져왔습니다.
```

⇒ 타입스크립트 타입 시스템은 자바스크립트의 런타임 동작을 ‘모델링’한다.

* 타입 체크를 통과하더라도 여전히 런타임에 오류가 발생할 수 있다. (아래 예제)

```jsx
const names = ['Alice', 'Bob'];
console.log(names[2].toUpperCase());

// TypeError: Cannot read property 'toUpperCase' of undefined
```

⇒ 타입스크립트가 이해하는 값의 타입과 실제 값에 차이가 있기 때문에 오류 발생

⇒ 타입 시스템이 정적 타입의 정확성을 보장해줄 것 같지만 그렇지 않다.

#### ✅요약

* ts는 js의 상위집합
* ts는 js 런타임 동작을 모델링하는 타입 시스템을 가지고 있기 때문에 런타임 오류를 발생시키는 코드를 찾아내려고 한다.
* ts 타입 시스템은 전반적으로 js 동작을 모델링한다.
