---
icon: '1'
---

# 블로그 서비스 최적화

### 실습 내용 소개

#### 최적화 기법

* 이미지 사이즈 최적화 `(로딩 성능 최적화)`: 어떤 이미지 사이즈가 적절한지 살펴보고, 블로그 사이트에 적용하여 성능을 높여보자.
* 코드 분할 `(로딩 성능 최적화)`: SPA 특성상 모든 리액트 코드가 하나의 자바스크립트 파일로 번들링되어 로드되기 때문에, 첫 페이지 진입 시 당장 사용하지 않는 코드가 다소 포함될 수 있기 때문에, 코드 분할을 통해 당장 필요 없는 코드는 떼어내고, 해당 코드를 필요한 시점에 따로 로드할 수 있다.
* 텍스트 압축 `(로딩 성능 최적화)`: HTML, CSS, 자바스크립트와 같은 리소스는 다운로드 전에 서버에서 미리 압축할 수 있다. 원래 사이즈보다 더 작은 사이즈로 다운로드하여 웹 페이지를 더 빠르게 로드할 수 있다.
* 병목 코드 최적화 `(렌더링 성능 최적화)`: 서비스를 느리게 만드는 코드를 병목 코드라고 하는데, 병목 코드를 어떻게 찾아내고, 어떤 방법으로 최적화할 수 있는지 알아보자.

#### 분석 툴

* 크롬 개발자 도구
* 크롬 개발자 도구의 Network 패널
* 크롬 개발자 도구의 Performance 패널
* 크롬 개발자 도구의 Lighthouse 패널
* webpack-bundle-analyzer: 직접 설치해야 하는 툴
  * webpack을 통해 번들링된 파일이 어떤 코드, 즉 어떤 라이브러리를 담고 있는지 보여준다.
  * 최종적으로 번들 파일 중 불필요한 코드가 어떤 코드이고, 번들 파일에서 어느 정도의 비중을 차지하고 있는지 확인할 수 있다.

### 서비스 탐색 및 코드 분석

#### 서비스 탐색

* 블로그 서비스
  * 타이틀, 게시물, 섬네일 등등
  * 딱 두 종류의 페이지
    * 목록 페이지
    * 상세 페이지

### Lighthouse 툴을 이용한 페이지 검사

#### Lighthouse로 검사하기

* Mode
  * Navigation: Lighthouse의 기본값, 초기 페이지 로딩 시 발생하는 성능 문제를 분석
  * Timespan: 사용자가 정의한 시간 동안 발생한 성능 문제를 분석
  * Snapshot: 현재 상태의 성능 문제를 분석
* Categories
  * Performance: 웹 페이지의 로딩 과정에서 발생하는 성능 문제를 분석
  * Accessiblity: 서비스의 사용자 접근성 문제를 분석
  * Best practices: 웹사이트의 보안 측면과 웹 개발의 최신 표준에 중점을 두고 분석
  * SEO: 검색 엔진에서 얼마나 잘 크롤링되고 검색 결과에 표시되는지 분석
  * Progressive Web App: 서비스 워커와 오프라인 동작 등, PWA와 관련된 문제를 분석

#### Lighthouse 검사 결과

웹 바이탈(Web Vitals): 여섯 가지 지표들

* **First Contentful Paint (FCP)**
  * 페이지가 로드될 때 브라우저가 DOM 콘텐츠의 첫 번째 부분을 렌더링하는 데 걸리는 시간에 관한 지표
  * FCP는 총점 계산 시, 10%의 가중치를 가진다.
* **Speed Index (SI)**
  * 페이지 로드 중에 콘텐츠가 시각적으로 표시되는 속도를 나타내는 지표
  * SI는 총점 계산 시, 10%의 가중치를 가진다.
* **Largest Contentful Paint (LCP)**
  * 페이지가 로드될 때 화면 내에 있는 가장 큰 이미지나 텍스트 요소가 렌더링되기까지 걸리는 시간을 나타내는 지표
  * LCP는 총점 계산 시, 25%의 가중치를 가진다.
* **Time to Interactive (TTI)**
  * 사용자가 페이지와 상호작용이 가능한 시점까지 걸리는 시간을 측정한 지표
    * 상호작용: 클릭 또는 키보드 누름 같은 사용자 입력을 의미
    * 즉, 이 시점 전까지는 화면이 보이더라도 클릭 같은 입력이 동작하지 않는다.
  * TTI는 총점 계산 시, 10%의 가중치를 가진다.
* **Total Blocking Time (TBT)**
  * 페이지가 클릭, 키보드 입력 등의 사용자 입력에 응답하지 않도록 차단된 시간을 총합한 지표
  * FCP와 TTI 사이의 시간동안 측정되며, 메인 스레드를 독점하여 다른 동작을 방해하는 작업에 걸리는 시간을 총합한다.
  * TBT는 총점 계산 시, 30%의 가중치를 가진다.
* **Cumulative Layout Shift (CLS)**
  * 페이지 로드 과정에서 발생하는 예기치 못한 레이아웃 이동을 측정한 지표
    * 레이아웃 이동: 화면상에서 요소의 위치나 크기가 순간적으로 변하는 것
  * CLS는 총점 계산 시, 15%의 가중치를 갖는다.
* **아래 Opportunities 섹션과 Diagnostics 섹션**
  * 웹페이지의 문제점과 해결 방안, 그리고 문제를 해결함으로써 얻을 수 있는 이점이 무엇인지 보여준다.
    * Opportunities: 페이지를 더욱 빨리 로드하는 데 잠재적으로 도움되는 제안 나열
    * Diagnostics: 로드 속도와 직접적인 관계는 없지만 성능과 관련된 기타 정보를 보여줌

### 이미지 사이즈 최적화

#### 비효율적인 이미지 분석

![image.png](attachment:f21271fd-6e64-469e-b8b9-9468e3c7678e:image.png)

* **Properly size images**
  * 이미지를 확인하려면 크롬 개발자 도구 중 Elements 패널로 이동해서 콘텐츠 요소 확인
  * 실제 이미지 사이즈(Intrinsic size)와 화면에 그려지는 이미지 사이즈(Rendered size)를 비교해보면
    * 실제 이미지 사이즈가 1200 x 1200px이어도, rendered size가 120 x 120px이면 그냥 처음부터 120 x 120px에 맞는 이미지를 사용하는 게 좋다.
    * 하지만, 요즘 사용되는 레티나 디스플레이는 같은 공간(픽셀)에 더 많은 픽셀을 그릴 수 있기 때문에, 너비 기준으로 두 배 정도 큰 이미지를 사용하는 것이 더 적절하다. ⇒ 즉, 240 x 240px 사이즈
  * 그럼 어떻게 줄일까?
    * 이미지가 어디서 오는지 먼저 파악해야 한다.
    *   API에서 받아오는 이미지 형태라면, 어떻게 조절해야 할까?

        ⇒ Cloudinary나 Imgix같은 이미지 CDN을 사용하는 방법

#### 이미지 CDN

CDN(Content Delivery Network)이란 물리적 거리의 한계를 극복하기 위해 소비자(사용자)와 가까운 곳에 콘텐츠 서버를 두는 기술을 의미한다.

ex) 미국에 있는 서버를 미리 한국으로 복사해두고, 사용자가 이미지를 다운로드하려고 할 때 미국 서버가 아닌 한국 서버에서 다운로드하도록 하는 것 → 물리적 거리가 어느정도 해소가 됐으니 다운로드에 걸리는 시간도 단축 → 일반적인 CDN의 개념

* 이미지 CDN은 이미지에 특화된 CDN이다.
  * 기본적인 CDN 기능 + 이미지를 사용자에게 보내기 전에 특정 형태로 가공하여 전해주는 기능까지 있음
    * ex) 이미지 사이즈를 줄이거나, 특정 포맷으로 변경하는 등의 작업
* 일반적인 이미지 CDN에서 제공하는 주소
  * [http://cdn.image.com?src=](http://cdn.image.com/?src=)\[img src]\&width=240\&height=240
  * 이미지 CDN 서버의 주소에 쿼리스트링으로 가져올 이미지 주소 또는 이름 입력 + 형태 명시

하지만.. 이미지 CDN은 직접 만들지 않는다. (블로그 서비스같은 거에서는)

```jsx

...
<img src={props.image + getParametersForUnsplash({width: 1200, height: 1200, quality: 80, format: 'jpg'})} alt="thumbnail" />
...
```

```jsx
function getParametersForUnsplash({width, height, quality, format}) {
  return `?w=${width}&h=${height}&q=${quality}&fm=${format}&fit=crop`
}
```

⇒ 이런식으로..

⇒ props:image: API를 통해 전달된 블로그 글의 섬네일 이미지

⇒ 여기 설정된 width와 height와 width를 통해 이미지 사이즈가 결정된다고 추측할 수 있다.

⇒ Unsplash 서비스의 이미지를 사용하고 있다면, 이 서비스가 일종의 이미지 CDN의 역할을 하는 느낌

#### 적절한 이미지 사이즈로 최적화

1200 x 1200px → 240 x 240px로 변경하려면?

```jsx

...
<img src={props.image + getParametersForUnsplash({width: 240, height: 240, quality: 80, format: 'jpg'})} alt="thumbnail" />
...
```

이미지 사이즈 최적화 후 점수가 올라가게 된다!

그리고 더이상 Opportunities 섹션에 ‘Properly size images’ 항목이 보이지 않게 된다.

### 병목 코드 최적화

#### Performance 패널 살펴보기

이번에는 Diagnostics 섹션의 ‘Reduce JavaScript execution time’ 항목을 살펴보자.

* **performance 패널을 사용하는 방법 2가지**
  * Lighthouse 결과 페이지에 ‘View Original Trace’라는 버튼이 있는데, 이 버튼을 눌러 Performance 패널로 이동하는 방법
    * 이 버튼을 눌러 이동하면, Lighthouse를 통해 분석한 내용을 Performance 패널로 가져가서 보여준다.
  *   직접 performance 패널로 이동하여 분석을 진행하는 방법

      * 네트워크 설정 ‘Fast 4G’, CPU 설정은 ‘No throttling’으로 유지

      ![image.png](attachment:aeadf0c6-441d-4c79-b661-3a01c332202e:image.png)

      **1) CPU 차트, Network 차트, 스크린샷 (좌측 상단)**

      CPU차트: 시간에 따라 CPU가 어떤 작업에 리소스를 사용하고 있는지 비율 확인 가능

      js(노란색), 렌더링/레이아웃 작업(보라색), 페인팅 작업(초록색), 기타 시스템 작업(회색)

      **빨간색 선 → 병목이 발생하는 지점 (특정 작업이 메인 스레드를 오랫동안 잡아두고 있다는 뜻)**

      **2) Network 타임라인**

      Network 타임라인: Network 패널과 유사하게 서비스 로드 과정에서의 네트워크 요청을 시간 순서에 따라 보여준다.

      **3) Frames, Timings, Main**

      Frames 섹션은 화면의 변화가 있을 때마다 스크린샷을 찍어 보여준다.

      Timings 섹션은 User Timing API를 통해 기록된 정보를 기록한다. (막대들은 각 컴포넌트의 렌더링 시간을 측정한 것)

      Main 섹션은 브라우저의 메인 스레드에서 실행되는 작업을 플레임 차트로 보여준다.

      \*플레임 차트란, 소프트웨어의 스택(작업)을 손쉽게 추적하기 위해 개발된 계층형 데이터 시각화 기법

      **4) 하단 탭**

      하단에 있는 Summary, Bottom-Up, Call Tree, Event Log 탭에서는 전체 또는 선택된 영역에 대한 상세 내용 확인 가능

#### 페이지 로드 과정 살펴보기

![image.png](attachment:1623a7fa-81a2-4f54-a80a-31294a086f63:image.png)

처음 localhost라는 네트워크 요청 (파란색): HTML 파일에 대한 요청을 의미

→ 이어서 bundle.js, 0.chunk.js, main.chunk.js 등의 파일 로드: 자바스크립트 파일에 대한 요청을 의미

→ 주목!!!! 0.chunk.js 로드 시간이 매우 길다는 문제점 (일단 체크하고 잠시 skip 나중에 다시 볼거다)

* 여기 이상한 점..!

⇒ Timing 섹션의 ArticleList 항목에 커서를 올리면, 실행 시간이 무려 1.4초인 걸 확인할 수 있다.

네트워크 시간을 포함한 시간이 아니라 모든 데이터가 준비된 상태에서 단순히 데이터를 화면에 그리는 것일 뿐인데 왜 이렇게 오래걸릴까?

→ 메인 스레드 .. 내려가면 ‘Article’ 작업이 있는데 이 아래로 또 내려가 보면..

→ removeSpecialCharacter 작업이 보인다.

**→ 이게 결국 Article 컴포넌트의 렌더링 시간을 길어지게 했다는 이야기.**

→ 얘를 성능 최적화 포인트로 잡고 개선해보자!!

```jsx
function removeSpecialCharacter(str) {
  const removeCharacters = [
    "#",
    "_",
    "*",
    "~",
    "&",
    ";",
    "!",
    "[",
    "]",
    "`",
    ">",
    "\\n",
    "=",
    "-",
  ];
  let _str = str;
  let i = 0,
    j = 0;

  for (i = 0; i < removeCharacters.length; i++) {
    j = 0;
    while (j < _str.length) {
      if (_str[j] === removeCharacters[i]) {
        _str = _str.substring(0, j).concat(_str.substring(j + 1));
        continue;
      }
      j++;
    }
  }

  return _str;
}
```

#### 병목 코드 개선

현재 코드를 보면..

* 특수 문자마다 반복문 돌리고.. 일치하는 문자열 찾고 제거하기 위해 반복문을 두번 중첩해서 사용중
* 문자열 제거하는 데도 substring과 concat 함수를 이용중

⇒ 상당히 비효율적

⇒ 자바스크립트에 이미 일치하는 문자를 찾아 제거해주는 replace 내장 함수가 있으니 얘를 활용하자.

**이 함수를 최적화하는 방법**

1. 특수 문자를 효율적으로 제거하는 로직으로 변경하는 방법
   1. 그냥 replace로 사용
2. 작업량을 줄이는 방법
   1. 어차피 목록에서는 대략 200자만 보여주니까, 200자만 잘라서 탐색하고 변경하는 방향으로

* `1번 방법 적용하기`

replace는 단일 문자에 적합하니.. 우린 정규표현식을 사용해야 한다.

```jsx
str = str.replace(/[#_*~&;![\\]`>\\n=\\->]/g, '');
```

* `2번 방법 적용하기`

필요한 내용만 잘라 사용하는 방법이다.

```jsx
function removeSpecialCharacter(str) {
	let _str = str.substring(0, 300); // 넉넉하게 300자 정도
	_str = str.replace(/[#_*~&;![\\]`>\\n=\\->]/g, '');
	return _str;
}
```

#### 최적화 전후 비교

1.4초 → 36밀리초로 줄어들었다.

특히! TTI와 TBT가 많이 줄어든 것을 확인할 수 있다.

추가로, Diagnostics 섹션에서 ‘Reduce JavaScript execution time’ 항목이 사라졌다.

### 코드 분할 & 지연 로딩

#### 번들 파일 분석

cra-bundle-analyzer은 내부적으로 webpack-bundle-anaylzer를 사용하는 툴로, 결과물은 동일하지만 Create React App 프로젝트에서 eject 없이 사용할 수 있다.

![image.png](attachment:6f5574ad-a77a-41ce-8356-d51ca902db92:image.png)

⇒ 블로그 서비스의 번들 분석 결과

⇒ 파일의 실제 크기에 따라 비율로 보여주기 때문에, 어떤 패키지가 어느 정도의 용량을 차지하고 있는지도 쉽게 알 수 있다.

* refractor 패키지의 출처
  * package-lock.json(또는 yarn.lock) 파일에 명시되어 있음
  *   dependencies라는 필드에서 @babel/core 패키지가 내부적으로 사용하고 있는, 즉 의존성을 가지고 있는 패키지를 확인할 수 있다.

      ⇒ 그래서 npm install을 하면 이 package-lock.json을 참조해서 설치하고자 하는 패키지가 어떤 버전의 패키지에 의존성이 있는지 확인해서 함께 설치한다.

![image.png](attachment:020a0b60-2018-4325-b2c2-5d93074760d5:image.png)

⇒ package-lock.json에서 refractor을 참조하고 있는 패키지

⇒ react-syntax-highlighter 패키지는 마크다운의 코드 블록에 스타일을 입히는 데 사용되는 라이브러리

⇒ src/components/markdowns/CodeBlock.js에서 사용중

**⇒ CodeBlock 컴포넌트는 마크다운을 표시하는 데 필요하니 블로그 글 상세페이지에서만 필요할 뿐, 글 목록 페이지에서는 필요가 없다!**

⇒ 즉, 크기가 너무 큰 react-syntax-highlighter 모듈은 블로그 글 상세 페이지에서만 사용되니 사용자가 처음 진입하는 목록 페이지에서는 굳이 다운로드할 필요가 없다.

⇒ 이 번들 파일을 페이지별로 필요한 내용만 부닐하여 로드하자.

.. 어떻게 해야 할까?

**코드 분할** 기법 사용!!

#### 코드 분할이란

* 코드 분할 → 말 그대로 코드를 분할하는 기법, 하나의 번들 파일을 여러 개의 파일로 쪼개는 방법
* 지연 로딩 → 분할된 코드는 사용자가 서비스를 이용하는 중 해당 코드가 필요해지는 시점에 로드되어 실행됨

![image.png](attachment:16063577-abad-4d8d-bdd3-d300f4c2a24d:image.png)

\*핵심은 !!

**⇒ 불필요한 코드 또는 중복되는 코드 없이 적절한 사이즈의 코드가 적절한 타이밍에 로드되도록 하는 것**

#### 코드 분할 적용하기

\[코드 분할을 하는 가장 좋은 방법 “동적 import를 사용하는 방법”]

```jsx
import { add } from './math';

console.log('1 + 4 = ', add(1, 4));
```

⇒ 이렇게 했을 때 모듈은 빌드 시에 함께 번들링된다.

```jsx
import('add').then((module) => {
	const { add } = module;
	
	console.log('1 + 4 = ', add(1, 4));
});
```

⇒ 이렇게 하면 빌드할 때가 아닌 런타임에 해당 모듈을 로드

⇒ 이런 방식을 **dynamic import**라고 한다!

→ 그런데.. 이 방식은 문제가 하나 있음

⇒ 동적 import 구문은 Promise 형태로 모듈을 반환해준다는 것. 따라서 여기서 import하려는 모듈은 컴포넌트이기 때문에 Promise 내부에서 로드된 컴포넌트를 Promise 밖으로 빼내야 한다.

⇒ 리액트는 다행히 이를 해결하기 위해 lazy와 Suspense를 제공한다. (이 함수 이용 시, 비동기 문제를 신경쓰지 않고 간편하게 동적 import 가능)

```jsx
import React, { Suspense } from 'react';

const SomeComponent = React.lazy(() => import('./SomeComponent'));

function MyComponent() {
	return (
		<div>
			<Suspense fallback={<div>Loading...</div>}>
				<SomeComponent />
			</Suspense>
		</div>
	)
}
```

* lazy: 동적 import를 호출하여 그 결과인 Promise를 반환하는 함수를 인자로 받는다.
  * lazy 함수가 반환한 값, 즉 import한 컴포넌트는 Suspense 안에서 렌더링해야 한다.
  * 만약 동적 import를 하는 동안 SomeComponent가 아직 값을 갖지 못할 때는 Suspense의 fallback prop에 정의된 내용으로 렌더링
  * 이후 SomeComponent가 온전히 로드됐을 때 fallback 값으로 렌더링된 Suspense가 정상적으로 SomeComponent를 렌더링

```jsx
import React, { Suspense, lazy } from 'react';
import { Switch, Route } from 'react-router-dome';
import './App.css';
// import ListPage from './pages/ListPage/index';
// import ViewPage from './pages/ViewPage/index';

const ListPage = lazy(() => import('./pages/ListPage/index'));
const ViewPage = lazy(() => import('./pages/ViewPage/index'));

function App() {
	return (
		<div className='App>
			<Suspense fallback={<div>Loading...</div>}>
				<Switch>
					<Route path='/' component={ListPage} exact />
					<Route path='/view/:id' component={ViewPage} exact />
				</Switch>
			</Suspense>
		</div>
	)
}
```

⇒ 각 페이지 컴포넌트들은 코드가 분할되고, 사용자가 목록 페이지에 접근했을 때 전체 코드가 아닌 ListPage 컴포넌트의 코드만 동적으로 import하여 화면을 띄우게 된다.

### 텍스트 압축

#### production 환경과 development 환경

*   cra의 경우 → production과 development 환경일 때 차이가 있다.

    * production 환경일 때 → webpack에서 경량화나 난독화(uglify)같은 추가적인 최적화 작업을 진행
    * development 환경일 때 → 이런 최적화 작업 없이 서비스 실행

    ⇒ 즉, 최종 서비스 성능을 측정할 때는 실제 사용자에게 제공되는 production환경으로 빌드된 서비스의 성능을 측정해야 한다.
* npm run serve
  * 먼저 빌드하고
  * 빌드가 완료되면 serve라는 라이브러리를 통해 /build 폴더에 있는 파일을 서비스
    * serve 라이브러리: 특정 파일 또는 폴더를 간단하게 웹에서 서비스할 수 있게 돕는 라이브러리
*   npm run start vs. npm run serve의 차이?

    ⇒ 일부 번들 파일의 사이즈에 큰 차이가 있다.
* **Opportunities 섹션의 “Enable text compression” 항목**
  * 서버로부터 리소스를 받을 때, 텍스트 압축을 해서 받으라는 의미

#### 텍스트 압축이란

말그래도 텍스트를 압축하는 것.

파일 압축 → 더 작은 크기로 빠르게 전송 → 사용하는 시점에 압축 해제

⇒ 압축한 만큼 파일 사이즈가 작아질 테니 리소스를 전송하는 시간도 단축된다.

압축 여부를 확인하려면? ⇒ HTTP의 헤더를 살펴보자.

ex. 블로그 사이트 내 API 데이터에 텍스트 압축이 적용되어 있는 상태

![image.png](attachment:9c35d014-b2d0-42f0-96a8-cd69f3db8be2:image.png)

⇒ Response Headers → Content-Encoding: `gzip` 형태 (압축된걸 확인 가능)

\[압축 방식] - 크게 2가지

* Gzip
  * 블록화, 휴리스틱 필터링, 헤더와 체크섬과 함께 내부적으로 Deflate를 사용하는 압축 방식
  * 더 좋은 압축률 제공
* Deflate
  * LZ77이라는 알고리즘과 허프먼 코딩을 사용해 데이터를 감싸는 매우 인기있는 압축 방식

#### 텍스트 압축 적용

텍스트 압축은 이 리소스를 제공하는 서버에서 설정해야 한다.

![image.png](attachment:36aa23f0-f767-4959-8bf5-42fc067322ed:image.png)

*   serve 내 -u와 -s 옵션 확인 가능

    * -s: SPA 서비스를 위해 매칭되지 않는 주소는 모두 index.html로 보내겠다는 옵션
    * -u: 텍스트 압축을 하지 않겠다는 옵션

    ⇒ 즉, 텍스트 압축을 위해서는 u 옵션만 제거하면 된다.
