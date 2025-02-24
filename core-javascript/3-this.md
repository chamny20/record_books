# 3️⃣ 3장 - this

* this는 클래스로 생성한 인스턴스 객체
* 함수와 객체의 구분이 느슨한 자바스크립트에서는 this는 실질적으로 이 둘을 구분하는 거의 유일한 기능

### 3-1 상황에 따라 달라지는 this

*   this는 기본적으로 **실행 컨텍스트가 생성될 때** 함께 결정된다.

    * 실행 컨텍스트는 함수를 호출할 때 생성되므로, 바꿔말하면 **this는 함수를 호출할 때 결정된다**고 할 수 있다.

    ⇒ 함수를 어떤 방식으로 호출하느냐에 따라 값이 달라지는 것

#### 3-1-1 전역 공간에서의 this

* 전역 공간에서 this → 전역 객체를 가리킨다.
  * 개념상 전역 컨텍스트를 생성하는 주체가 바로 전역 객체이기 때문이다.
  * 런타임 환경에 따른 전역 객체
    * 브라우저 환경 → window
    * Node.js 환경 → global

```jsx
// 전역 공간에서의 this(브라우저 환경)
console.log(this);
console.log(window);
console.log(this === window); //true
```

```jsx
// 전역 공간에서의 this(Node.js 환경)
console.log(this);
console.log(global);
console.log(this === global); //true
```

* **전역변수 선언 시, 자바스크립트 엔진은 이를 전역객체의 프로퍼티로도 할당**

```jsx
var a = 1;
window.b = 2;
console.log(a, window.a, this.a); // 1 1 1
console.log(b, window.b, this.b); // 2 2 2
```

**자바스크립트의 모든 변수는 특정 객체의 프로퍼티로서 동작한다.**

⇒ 사용자가 var 연산자를 이용해 변수를 선언하더라도 실제 자바스크립트 엔진은 어떤 특정 객체의 프로퍼티로 인식하는 것

⇒ 전역 스코프의 L.E, 즉 전역객체에서 해당 프로퍼티 a를 발견해서 그 값을 반환한다.

* **특정 객체: 실행 컨텍스트의 LexicalEnvironment이다.**

```jsx
var a = 1;
window.b = 2;
console.log(a, window.a, this.a); // 1 1 1
console.log(b, window.b, this.b); // 2 2 2

window.a = 3;
b = 4;
console.log(a, window.a, this.a); // 3 3 3 
console.log(b, window.b, this.b); // 4 4 4
```

* ‘삭제’ 명령

```jsx
var a = 1;
delete window.a; // (delete a;) false
console.log(a, window.a, this.a); // 1 1 1
---
var b = 2;
delete b; // false
console.log(b, window.b, this.b); // 2 2 2
---
window.b = 3;
delete window.c; // (delete c;) true
console.log(c, window.c, this.c); // Uncauht ReferenceError: c in not defined
---
window.d = 4;
delete d; // (delete d;) true
console.log(d, window.d, this.d); // Uncauht ReferenceError: d in not defined
```

⇒ 처음부터 전역객체의 프로퍼티로 할당한 경우에는 삭제가 되는 반면, 전역변수로 선언한 경우에는 삭제가 되지 않는다.

**⇒ 즉 전역변수를 선언하면 자바스크립트 엔진이 이를 자동으로 전역객체의 프로퍼티로 할당하면서 추가적으로 해당 프로퍼티의 configurable 속성을 false로 정의하는 것**

⇒ 정리하면, var로 선언한 전역변수와 전역객체의 프로퍼티는 호이스팅 여부 및 configurable 여부에서 차이를 보인다.

#### 3-1-2 메서드로서 호출할 때 그 메서드 내부에서의 this

* **함수 vs. 메서드**
  *   함수를 실행하는 방법

      * 함수로서 호출하는 경우
      * 메서드로서 호출하는 경우

      ⇒ 이 둘을 구분하는 유일한 차이: `독립성`
  * 함수
    * 그 자체로 독립적인 기능 수행
  * 메서드
    * 자신을 호출한 대상 객체에 관한 동작을 수행

```jsx
var func = function (x) {
	console.log(this, x);
};
func(1); // window { ... } 1
// => 앞에 객체가 명시가 되어있지 않는 모든 경우는 "함수"로 호출한 것

var obj = {
	method: func
};

obj.method(2); // { method: f } 2
obj['method'](2); // { method: f } 2
// => 이렇게 앞에 객체가 명시돼 있는 경우는 "메서드"로 호출한 것
```

⇒ 원래의 익명함수는 그대로인데 이를 **변수에 담아 호출한 경우**와 **obj 객체의 프로퍼티에 할당해서 호출한 경우**에 this가 달라지는 것

* **메서드 내부에서의 this**

this → 호출한 주체에 대한 정보가 담긴다.

메서드의 호출 주체는 바로 함수명(프로퍼티명) 앞의 “객체”이다.

```jsx
var obj = {
    methodA: function(){
        console.log(this);
    },
    inner: {
        methodB: function(){
            console.log(this);
        }
    }
};

// methodA 호출 주체는 obj
obj.methodA(); // obj
obj['methodA'](); // obj`

// methodB 호출 주체는 inner
obj.inner.methodB(); // obj.inner
obj.inner['methodB'](); // obj.inner
obj['inner'].methodB(); // obj.inner
obj['inner']['methodB'](); // obj.inner
```

#### 3-1-3 함수로서 호출할 때 그 함수 내부에서의 this

* 함수 내부에서의 this
  * 함수로서 호출하는 것은 호출 주체(객체지향언어에서의 객체)를 명시하지 않고 개발자가 코드에 직접 관여해서 실행한 것이기 때문에 호출 주체의 정보를 알 수 없는 것
* 메서드의 내부함수에서의 this
  * 내부 함수 역시 이를 함수로서 호출했는지, 메서드로서 호출했는지만 파악하면 this의 값을 정확히 맞출 수 있다.
  * 메서드 내부의 함수에서도 함수로서 호출되는 경우는 전역 객체를 가리킨다.

```jsx
var obj1 = {
    outer: function() {
        console.log(this); // (1)
        
        var innerFunc = function() {
            console.log(this); // (2) (3)
        }
        
        innerFunc(); //window (전역객체)
        
        var obj2 = {
            innerMethod: innerFunc
        };
        
        obj2.innerMethod(); //obj2
    }
};

obj1.outer(); //obj1
```

1. `obj1.outer()` 호출 → 실행 컨텍스트 생성되면서 호이스팅, 스코프 체인 정보 수집, this 바인딩한다. 이 함수는 호출할 때 함수명인 outer 앞에 점이 있었으므로 메서드로서 호출한 것. 따라서 this에는 마지막 점 앞의 객체인 obj1이 바인딩된다.
2. (1)에서 obj2 객체 정보가 출력된다.
3. 호이스팅된 변수 innerFunc()는 outer 스코프 내에서만 접근할 수 있는 지역변수이다. 이 지역변수에 익명함수 할당 후, innerFunc() 호출
4. innerFunc 함수의 실행 컨텍스트가 생성되면서 호이스팅, 스코프 체인 수집, this 바인딩 등을 수행한다. 함수 호출 시 함수명 앞에 점이 없으므로 **“함수로서 호출한 것” → this가 지정되지 않았고, 따라서 자동으로 스코프 체인상의 최상위 객체인 전역객체(window)가 바인딩된다.**
5. (2)에서 Window 객체 정보가 출력된다.
6. 호이스팅된 obj2 변수 역시 outer 스코프 내에서만 접근할 수 있는 지역변수이다. 여기에는 다시 객체를 할당하는데, 그 객체에는 innerMethod라는 프로퍼티가 있으며, 여기에는 앞서 정의된 innerFunc와 연결된 익명 함수가 연결된다.
7. obj2.innerMethod() 호출 → 실행 컨텍스트 생성 → 함수 호출 시 앞에 점이 있었으므로 **“메서드로서 호출”한 것. → this에는 마지막 점 앞의 객체인 obj2가 바인딩된다.**

⇒ 결론: **this 바인딩에 관해서는** 함수를 실행하는 당시의 주변 환경(메서드 내부인지, 함수 내부인디 등)은 중요하지 않고, **오직 해당 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지가 관건이다.**

* **메서드의 내부 함수에서의 this를 우회하는 방법**

변수를 검색하면 우선 가장 가까운 스코프의 L.E를 찾고 없으면 상위 스코프를 탐색하듯….

→ this 역시 현재 컨텍스트에 바인딩된 대상이 없으면 직전 컨텍스트의 this를 바라보도록 하는 게 어떨까…?

→ this를 우회하는 대표적인 방법: 바로 변수를 활용하는 것

```jsx
var obj1 = {
    outer: function() {
        console.log(this); // (1) { outer: f }
        
        var innerFunc = function() {
            console.log(this); // (2) Window { ... }
        }
        
        innerFunc(); 
        
        var self = this;
        var innerFunc2 = function() {
            console.log(self); // (3) { outer: f }
        }
        
        innerFunc2(); 
    }
};

obj1.outer(); 
```

⇒ self: 상위 스코프의 this를 저장해서 내부함수에서 활용하려는 수단

*   **this를 바인딩하지 않는 함수**

    * ES6에서는 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하고자, this를 바인딩하지 않는 “화살표 함수”를 새로 도입했다.
    * 화살표 함수: 실행 컨텍스트를 생성할 때 this 바인딩 과정 자체가 빠지게 되어, 상위 스코프의 this를 그대로 활용할 수 있다.

    ```jsx
    var obj1 = {
        outer: function() {
            console.log(this); // (1) { outer: f }
            
            var innerFunc = () => {
                console.log(this); // (2) { outer: f }
            }
            
            innerFunc(); //obj1 (상위 스코프의 this)
            
        }
    };

    obj1.outer(); //obj1
    ```

#### 3-1-4 콜백 함수 호출 시 그 함수 내부에서의 this

**함수 A의 제어권을 다른 함수(or method) B에게 넘겨주는 경우** → **함수 A를 콜백 함수**라고 한다.

⇒ 함수 A는 함수 B의 내부 로직에 따라 실행되며, this 역시 함수 B 내부 로직에서 정한 규칙에 따라 값이 결정된다.

```jsx
// 0.3초 뒤에 전역객체 출력
setTimeout(function() {  // (1)
    console.log(this);   //window
}, 300); 

// 전역객체와 배열의 각 요소 출력
[1, 2, 3, 4, 5].forEach(function(x) { // (2)
    console.log(this, x); //window, eachElement
});

// addEventListener는 콜백 함수를 호출할 때 자신의 this를 상속하도록 지정되어 있음
document.body.innerHTML += `<button id="a">클릭</button>`;
document.body.querySelector('#a')
    .addEventListener('click', function(e) { // (3)
        console.log(this, e); // button element, 이벤트 객체
    });
```

(1): setTimeout 함수는 300ms만큼 시간 지연을 한 뒤, 콜백 함수를 실행하라는 명령 → 0.3초 뒤 전역객체가 출력된다.

(2): 전역 객체와 배열의 각 요소가 총 5회 출력된다.

(3): addEventListener: 지정한 HTMl element에 click 이벤트가 발생할 때마다 그 이벤트 정보를 콜백 함수의 첫 번째 인자로 삼아 함수를 실행하라는 명령이다. 버튼을 클릭하면 앞서 지정한 엘리먼트와 클릭 이벤트에 관한 정보가 담긴 객체가 출력된다.

⇒ (1)과 (2)에선 내부에서 콜백 함수를 호출할 때 대상이 될 this를 지정하지 않는다.

⇒ (3)의 addEventListener 메서드는 콜백 함수를 호출할 때 자신의 this를 상속하도록 정의돼 있다. 즉, 메서드명의 점 앞부분이 곧 this가 되는 것.

#### 3-1-5 생성자 함수 내부에서의 this

* 생성자 함수는 어떤 공통된 성질을 지니는 객체들을 생성하는 데 사용하는 함수
  * 객체지향 언어에서는) 생성자를 클래스, 클래스를 통해 만든 객체를 인스턴스라고 한다.
* 프로그래밍적 → **생성자: 구체적인 인스턴스를 만들기 위한 일종의 틀**
* `new` 명령어와 함께 함수를 호출하면 해당 함수가 생성자 함수로서 동작한다.
* 생성자 함수로서 호출되는 경우 내부에서의 this는 곧 새로 만들 구체적인 인스턴스 자신이 된다.

생성자 함수를 호출 → 생성자의 `prototype` 프로퍼티를 참조하는 `__proto__`라는 프로퍼티가 있는 객체(인스턴스) 생성 → 미리 준비된 공통 속성 및 개성을 해당 객체(this)에 부여

```jsx
var Cat = function(name, age){
    this.bark = '야옹';
    this.name = name;
    this.age = age;
};

var choco = new Cat('초코', 7);
var nabi = new Cat('나비', 5);

//Cat의 this는 각각 전달된 인스턴스 객체를 가리킴
console.log(choco); // Cat { bark: '야옹, name: '초코', age: 7 }
console.log(nabi); //Cat { bark: '야옹, name: '나비', age: 5 }
```

### 3-2 명시적으로 this를 바인딩하는 방법

앞서 이야기한 규칙을 깨고 this에 별도의 대상을 바인딩하는 방법도 있다.

상황에 따라 달라지는 this를 원하는 값으로 바인딩하는 방법을 알아보자.

#### 3-2-1 call 메서드

```jsx
Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```

* call 메서드: 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령
  * 첫 번째 인자를 this로 바인딩, 이후의 인자들을 호출할 함수의 매개변수로 전달
  * call을 사용하면 임의의 객체를 this로 지정할 수 있다.

```jsx
var func = function (a, b, c) {
  console.log(this, a, b, c);
}

func(1, 2, 3); // window{ ... } 1 2 3
func.call({x: 1}, 4, 5, 6); // {x: 1} 4 5 6
```

```jsx
var obj = {
	a: 1,
	method: function (x, y) {
		console.log(this.a, x, y);
	}
};

obj.method(2, 3); // 1 2 3
obj.method.call({ a: 4 }, 5, 6); // 4 5 6
```

#### 3-2-2 apply 메서드

```jsx
Function.prototype.apply(thisArg[, argsArray])
```

* 기능적으로는 call과 동일한 역할 수행
* 두 번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 지정한다는 점에서만 차이가 있다.

```jsx
var func = function (a, b, c) {
  console.log(this, a, b, c);
}

func.apply({x: 1}, [4, 5, 6]); // {x: 1} 4 5 6

var obj = {
	a: 1,
	method: function (x, y) {
		console.log(this.a, x, y);
	}
};

obj.method.apply({ a: 4 }, [5, 6]); // 4 5 6
```

#### 3-2-3 call / apply 메서드의 활용

* **유사배열객체에 배열 메서드를 적용**

```jsx
var obj = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3,
}

// obj에 push 메서드 호출하여 요소 추가
Array.prototype.push.call(obj, 'd')
console.log(obj) // {0: "a", 1: "b", 2: "c", 3: "d", length: 4}

// obj를 배열로 변환하여 arr에 저장
var arr = Array.prototype.slice.call(obj)
console.log(arr) // ["a", "b", "c", "d"]
```

⇒ 객체에는 배열 메서드를 직접 적용할 수 없다.

⇒ 키가 0 또는 양의 정수인 프로퍼티가 존재하고 length 프로퍼티의 값이 0 또는 양의 정수인 객체, 즉 배열의 구조와 유사한 객체의 경우 call or apply 메서드를 이용 → 배열 메서드를 차용 가능

\*slice ⇒ 매개변수를 아무것도 넘기지 않을 경우에 그냥 원본 배열의 얕은 복사본을 반환

+arguments, NodeList에 배열 메서드를 적용

\*단, 문자열의 경우 length property가 읽기 전용 → 원본 문자열에 변경을 가하는 메서드(push, pop, shift, unshift, splice 등)는 에러를 던지며, concat처럼 대상이 반드십 ㅐ열이어야 하는 경우 에러는 나진 않지만 제대로 된 결과 얻을 수 없다.

* **Array.from**

```jsx
var obj = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3,
}

var arr = Array.from(obj)
console.log(arr) // ["a", "b", "c"]
```

* **생성자 내부에서 다른 생성자를 호출**

생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call or apply 활용 → 다른 생성자를 호출하면 간단하게 반복을 줄일 수 있다.

```jsx
// Student, Employee 생성자 함수 내부에서 Person 생성자 함수를 호출해서 인스턴스 속성을 정의
// → 반복 줄어든다.

function Person(name, gender) {
	this.name = name;
	this.gender = gender;
}

function Student(name, gender, school) {
	Person.call(this, name, gender);
	this.school = school;
}

function Employee(name, gender, company) {
	Person.apply(this, [name, gender]);
	this.company = company;
}

var by = new Student('보영', 'fmale', '단국대');
var jn = new Employee('재난', 'male', '구골');
```

* **여러 인수를 묶어 하나의 배열로 전달하고 싶을 때 - apply 활용**

ex. 배열에서 최대/최솟값을 구해야 할 경우 apply를 사용하지 않는다면 부득이하게 다음과 같은 방식으로 직접 구현할 수밖에 없을 것이다.

```jsx
var numbers = [10,20,3,16,45];
// var max = Math.max.apply(null, numbers);
// var min = Math.min.apply(null, numbers);
const max = Math.max(...numbers); // ES6의 펼치기 연산자 활용 (spread operator)
const min = Math.min(...numbers);
console.log(max, min);  // 45 3
```

#### 3-2-4 bind 메서드

```jsx
Function.prototype.bind(thisArg[, arg1[, arg2[, ...]]])
```

ES5에서 추가된 기능으로, call과 비슷하지만 즉시 호출하진 않고 넘겨받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드이다.

⇒ 즉 bind 메서드는 **함수에 this를 미리 적용하는 것**과 **부분 적용 함수를 구현하는 두 가지 목적**을 모두 지닌다.

```jsx
var func = function (a, b, c, d){
	console.log(this, a, b, c, d);
};
func(1, 2, 3, 4);  // window{ ... } 1 2 3 4

var bindFunc1 = func.bind({x: 1});
bindFunc1(5, 6, 7, 8);  // { x: 1 } 5 6 7 8

var bindFunc2 = func.bind({ x: 1 }, 4, 5);
bindFunc2(6, 7);  // { x: 1 } 4 5 6 7
bindFunc2(8, 9);  // { x: 1 } 4 5 8 9
```

* **name 프로퍼티**

bind 메서드를 적용해서 새로 만든 함수는 한 가지 독특한 성질이 있다.

⇒ name 프로퍼티에 동사 bind의 수동태인 \*\*`bound`\*\*라는 접두어가 붙는다는 점!

⇒ `‘bound xxx’`: 함수명이 xxx인 원본 함수에 bind 메서드를 적용한 새로운 함수라는 의미

```jsx
var func = function (a, b, c, d){
	console.log(this, a, b, c, d);
};

var bindFunc = func.bind({ x: 1 }, 4, 5);
console.log(func.name);  // func
console.log(bindFunc.name);  // bound func
```

* **상위 컨텍스트의 this를 내부함수나 콜백 함수에 전달하기**

이전에 self 사용해서 활용한 우회법…보다 call, apply or bind 사용하면 더 깔끔하게 처리가 가능하다.

```jsx
var obj = {
	outer: function () {
		console.log(this);
		var innerFunc = function () {
			console.log(this); // { outer: f }
		};
		innerFunc.call(this); // { outer: f }
	}
};

obj.outer();
```

```jsx
var obj = {
	outer: function () {
		console.log(this); // { outer: f }
		
		var innerFunc = function () {
			console.log(this); // { outer: f }
		}.bind(this);
		
		innerFunc();
	}
};

obj.outer();
```

+bind메서드 - 내부함수에 this 전달

```jsx
var obj = {
	logThis: function () {
		console.log(this);
	},
	logThisLater1: function () {
		setTimeout(this.logThis, 500);
	},
	logThisLater2: function () {
		setTimeout(this.logThis.bind(this), 1000);
	}
};

obj.logThisLater1(); // Window { ... } - 전역 객체
obj.logThisLater2(); // obj { logThis: f, ... }
```

#### 3-2-5 화살표 함수의 예외사항

ES6에 새롭게 도입된 화살표 함수 → 실행 컨텍스트 생성 시 this를 바인딩하는 과정이 제외됐다.

즉, 이 함수 내부에는 this가 아예 없으며, 접근하고자 하면 스코프체인상 가장 가까운 this에 접근하게 된다.

```jsx
var obj1 = {
    outer: function() {
        console.log(this); // { outer: f }
        
        var innerFunc = () => {
            console.log(this); // { outer: f }
        }
        
        innerFunc(); 
    }
};

obj1.outer(); 
```

#### 3-2-6 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)

콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체(**thisArg**)를 인자로 지정할 수 있는 경우가 있는데, 이러한 메서드의 thisArg 값을 지정하면 콜백 함수 내부에서 this 값을 원하는 대로 변경할 수 있다.

이런 형태는 여러 내부 요소에 대해 같은 동작을 반복 수행해야 하는 **배열 메서드**에 많이 포진돼 있다.

* 대표적인 예 - forEach

```jsx
var report = {
  sum: 0,
  count: 0,
  add: function () {
    var args = Array.prototype.slice.call(arguments);
    args.forEach(function (entry) {
      this.sum += entry;
      ++this.count;
    }, this);
  },
  average: function () {
    return this.sum / this.count;
  },
};
report.add(60, 85, 95);
console.log(report.sum, report.count, report.average()); // 240 3 80
```

⇒ 콜백 함수 내부에서의 this → add 메서드에서의 this가 전달된 상태이므로 add 메서드의 this(report)를 그대로 가리키게 된다.

⇒ 따라서, 배열의 세 요소를 순회하면서 report.sum, report.count 값이 차례로 변경

⇒ 순회 마친 결과 출력됨

```jsx
// 콜백 함수와 함께 thisArg를 인자로 받는 메서드
Array.prototype.forEach(callback[, thisArg])
Array.prototype.map(callback[, thisArg])
Array.prototype.filter(callback[, thisArg])
Array.prototype.some(callback[, thisArg])
Array.prototype.every(callback[, thisArg])
Array.prototype.find(callback[, thisArg]
```

### 3-3 정리

* **명시적 this 바인딩이 없는 한 늘 성립하는 개념**
  * 전역공간에서의 this는 전역객체(브라우저에서는 window, Node.js에서는 global)를 참조한다.
  * 어떤 함수를 메서드로서 호출한 경우 this는 메서드 호출 주체(메서드명 앞의 객체)를 참조한다.
  * 어떤 함수를 함수로서 호출한 경우 this는 전역객체를 참조한다.
  * 콜백 함수 내부에서의 this는 해당 콜백 함수의 제어권을 넘겨받은 함수가 정의한 바에 따르며, 정의하지 않은 경우에는 전역객체를 참조한다.
  * 생성자 함수에서의 this는 생성될 인스턴스를 참조한다.
* **명시적 this 바인딩**
  * call, apply 메서드는 this를 명시적으로 지정하면서 함수 or 메서드를 호출한다.
  * bind 메서드는 this 및 함수에 넘길 인수를 일부 지정해서 새로운 함수를 만든다.
  * 요소를 순회하면서 콜백 함수를 반복 호출하는 내용의 일부 메서드는 별도의 인자로 this를 받기도 한다.
