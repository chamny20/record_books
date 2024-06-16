# ITEM2

### ITEM2 타입스크립트 설정 이해하기

타입스크립트 컴파일러는 매우 많은 설정을 가지고 있다. 현재 시점에는 설정이 거의 200개에 이른다.

다음 커맨드를 통해 설정 사용 가능.

* `$ tsc —noImplicitAny program.ts`
* or `tsconfig.json` 설정 파일 ⇒ 권장(서로 확인 및 공유 가능하기 때문)

설정을 제대로 사용하려면 다음 세팅을 이해해야 한다.

* noImplicitAny
* strictNullChecks

💡**noImplicitAny:** 변수들이 미리 정의된 타입을 가져야 하는지 여부를 제어한다.

```jsx
function add(a, b) {
	return a + b;
}
```

* noImplicitAny가 해제되어 있는 경우: 모든 타입이 any로 추론됨 (암시적==implicit any)
* noImplicitAny가 설정되어 있는 경우: 오류 발생

**⇒ ts는 타입 정보를 가질 때 가장 효과적이기 때문에, 되도록이면 noImplicitAny를 설정해야 한다.**

⇒ ts 문제 발견이 수월, 가독성 up, 개발자의 생산성 향상

💡**strictNullChecks:** null과 undefined가 모든 타입에서 허용되는지 확인하는 설정

```jsx
const x: number = null; 
```

* 해제된 경우: 정상, null은 유효한 값
* 설정된 경우: ‘null’ 형식은 ‘number’ 형식에 할당할 수 없습니다.

⇒ 이 설정은 null과 undefined 관련된 오류를 잡아내는 데 많은 도움이 되지만, 코드 작성을 어렵게 한다.

**⇒ 해당 설정을 권장하지만, ts가 처음이거나 js 코드를 마이그레이션 중이라면 설정하지 않아도 괜찮다. (가능한 프로젝트 초반에 설정하는 게 좋다!)**

`❗이 모든 체크를 설정하고 싶다면 strict 설정을 하면 된다. 그러면 대부분의 오류를 잡아낼 수 있다.`

#### ✅요약

* ts 컴파일러는 언어의 핵심 요소에 영향을 미치는 몇 가지 설정을 포함하고 있다.
* ts 설정을 커맨드 라인을 이용하기보다는 tsconfig.json을 사용하자.
* js를 ts로 전환하는 게 아니라면 noImplicitAny를 설정하자.
* ‘undefined는 객체가 아닙니다.’같은 런타임 오류를 방지하기 위해 strictNullChecks를 설정하는 게 좋다.
* ts에서 엄격한 체크를 하고 싶다면 strict 설정을 고려해야 한다.
