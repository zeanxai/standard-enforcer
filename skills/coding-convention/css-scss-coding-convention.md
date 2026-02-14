# CSS/SCSS Coding Convention

> **CSS Guidelines (Harry Roberts) + Sass Guidelines (Kitty Giraudel)** 기반
>
> - 공식 문서: https://cssguidelin.es/ , https://sass-guidelin.es/
> - 참고: https://google.github.io/styleguide/htmlcssguide.html , https://github.com/airbnb/css
> - 도구: https://stylelint.io/ (stylelint-config-standard-scss)
> - 관리: Harry Roberts (CSS Wizardry) / Kitty Giraudel

---

## 1. 선정 사유

| 기준 | CSS Guidelines + Sass Guidelines | Google HTML/CSS | Airbnb CSS/Sass | SMACSS |
|---|---|---|---|---|
| 포괄성 | 매우 높음 (CSS + SCSS 전체) | CSS 기본만 | 중간 (SCSS 포함) | 아키텍처 중심 |
| 커뮤니티 채택 | 글로벌 표준 수준 | Google 내부 중심 | React 생태계 | 제한적 |
| 최신성 | 활발 유지보수 | 유지보수 | 2023년 이후 중단 | 레거시 |
| SCSS 지원 | 네이티브 | 없음 | 부분적 | 없음 |
| 도구 지원 | Stylelint 완전 통합 | 없음 | 부분적 | 없음 |
| 아키텍처 가이드 | ITCSS 기반 | 없음 | BEM 변형 | SMACSS 자체 |

CSS Guidelines는 선택자, 특이성, 아키텍처를 포괄하는 가장 상세한 CSS 가이드이며, Sass Guidelines는 SCSS에 특화된 유일한 포괄적 가이드이다. 두 가이드의 조합은 업계 표준으로 널리 인정받는다.

---

## 2. 소스 파일 기본 규칙

### 2.1 파일명

| 규칙 | 상세 |
|---|---|
| 케이스 | `kebab-case` 소문자 하이픈 구분 |
| SCSS 파셜 | 언더스코어 접두사: `_variables.scss`, `_mixins.scss` |
| 컴포넌트 파일 | 컴포넌트명과 일치: `_button.scss`, `_card.scss` |

```
# 올바름
_variables.scss
_button.scss
_header.scss
main.scss

# 잘못됨
Variables.scss
Button.SCSS
myStyles.css
```

### 2.2 파일 인코딩

- UTF-8 필수
- `@charset "UTF-8";` 선언 (SCSS 메인 파일)

### 2.3 들여쓰기

| 규칙 | 상세 |
|---|---|
| 방식 | 스페이스 (탭 금지) |
| 크기 | **2스페이스** |

### 2.4 줄 길이

- **80자** 이하 권장
- URL 등 분리 불가한 값은 예외

---

## 3. 포매팅

### 3.1 선언 블록

```scss
// 올바름
.selector-a,
.selector-b,
.selector-c {
  display: block;
  color: #333;
}

// 잘못됨 — 선택자를 한 줄에 나열
.selector-a, .selector-b, .selector-c {
  display: block;
}

// 잘못됨 — 여는 중괄호 앞 공백 없음
.selector{
  display: block;
}
```

| 규칙 | 상세 |
|---|---|
| 여는 중괄호 | 선택자와 같은 줄, 앞에 공백 1개 |
| 닫는 중괄호 | 별도 줄, 선택자와 같은 인덴트 수준 |
| 다중 선택자 | 각 선택자를 별도 줄에 작성 |
| 세미콜론 | 마지막 선언 포함 **모든 선언에 필수** |
| 콜론 뒤 공백 | 필수 (`color: red`), 콜론 앞 공백 없음 |
| 규칙셋 사이 | 빈 줄 1개 |

### 3.2 속성 선언 순서

속성은 **유형별 그룹** 순서로 정렬한다:

```scss
.selector {
  // 1. 포지셔닝
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: z('default');

  // 2. 디스플레이 & 박스 모델
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow: hidden;
  box-sizing: border-box;
  width: 100px;
  height: 100px;
  padding: 10px;
  border: 1px solid #333;
  margin: 10px;

  // 3. 타이포그래피
  font-family: sans-serif;
  font-size: 1rem;
  font-weight: 700;
  line-height: 1.5;
  color: #333;
  text-align: center;
  text-transform: uppercase;

  // 4. 시각적 / 장식
  background-color: #fff;
  border-radius: 0.25rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  opacity: 1;

  // 5. 애니메이션
  transition: transform 0.3s ease;
  animation: fade-in 0.5s ease;

  // 6. 기타
  cursor: pointer;
  pointer-events: auto;
}
```

| 그룹 순서 | 속성 범주 |
|---|---|
| 1 | Positioning: `position`, `top`, `right`, `bottom`, `left`, `z-index` |
| 2 | Box Model: `display`, `flex-*`, `grid-*`, `overflow`, `box-sizing`, `width`, `height`, `padding`, `border`, `margin` |
| 3 | Typography: `font-*`, `line-height`, `color`, `text-*`, `letter-spacing` |
| 4 | Visual: `background-*`, `border-radius`, `box-shadow`, `opacity` |
| 5 | Animation: `transition`, `animation`, `transform` |
| 6 | Misc: `cursor`, `pointer-events`, `user-select`, `will-change` |

### 3.3 값 표기

| 규칙 | 올바름 | 잘못됨 |
|---|---|---|
| 앞자리 0 필수 | `opacity: 0.5;` | `opacity: .5;` |
| 0에 단위 금지 | `margin: 0;` | `margin: 0px;` |
| 소문자 hex | `color: #aabbcc;` | `color: #AABBCC;` |
| 축약 hex 사용 | `color: #fff;` | `color: #ffffff;` |
| 따옴표 | 작은따옴표 (`'`) 사용 | 큰따옴표 (`"`) |
| URL 따옴표 | `url('path/to/file')` | `url(path/to/file)` |
| border 제거 | `border: 0;` | `border: none;` |

---

## 4. 선택자 규칙

### 4.1 선택자 기본 원칙

| 규칙 | 상세 |
|---|---|
| ID 선택자 금지 | 스타일링에 `#id` 사용 금지. 클래스만 사용 |
| 타입 한정 금지 | `div.container` 금지 → `.container` 사용 |
| 선택자 깊이 | 최대 **3단계** (이상적으로 1~2단계) |
| 범용 선택자 | `*` 단독 사용 자제 |

```scss
// 올바름
.site-nav { }
.site-nav__item { }

// 잘못됨 — ID 선택자
#header { }
#navigation { }

// 잘못됨 — 타입 한정
ul.nav { }
div.container { }
input.btn { }

// 잘못됨 — 과도한 깊이
body .page .content .article .title { }
```

### 4.2 선택자 의도 (Selector Intent)

선택자는 **대상을 명확히** 표현해야 한다:

```scss
// 잘못됨 — 너무 광범위, header 안의 모든 ul을 대상으로 함
header ul { }

// 올바름 — 명시적 의도
.site-nav { }
```

### 4.3 위치 독립성

컴포넌트는 **위치에 의존하지 않아야** 한다:

```scss
// 잘못됨 — 위치 의존적
.sidebar .widget { }
.footer .widget { }

// 올바름 — 어디에든 배치 가능
.widget { }
.widget--compact { }
```

### 4.4 JavaScript 연결 클래스

```html
<!-- 스타일과 동작을 분리 -->
<button class="btn btn--primary js-submit-form">제출</button>
```

- `.js-` 접두사 클래스는 **절대 스타일링하지 않음**
- JavaScript 바인딩 전용

---

## 5. 네이밍 컨벤션

### 5.1 BEM (Block Element Modifier)

| 구분 | 패턴 | 예시 |
|---|---|---|
| Block | `.block-name` | `.search-form` |
| Element | `.block-name__element` | `.search-form__input` |
| Modifier | `.block-name--modifier` | `.search-form--dark` |
| Element + Modifier | `.block-name__element--modifier` | `.search-form__input--disabled` |

```scss
// 올바름
.card { }
.card__header { }
.card__body { }
.card__footer { }
.card--featured { }
.card__header--highlighted { }

// 잘못됨 — 요소의 요소 중첩
.card__header__title { }    // → .card__title 사용

// 잘못됨 — modifier만 단독 사용
<div class="card--featured">  // → <div class="card card--featured">
```

**핵심 규칙:**
- 모든 클래스명은 **소문자 + 하이픈** (kebab-case)
- Block과 Element는 `__` (더블 언더스코어)로 구분
- Block/Element와 Modifier는 `--` (더블 하이픈)으로 구분
- Element의 Element 중첩 금지: `.block__elem1__elem2` → `.block__elem2`
- Modifier는 항상 원래 클래스와 함께 사용

### 5.2 상태 클래스

```scss
// SMACSS 스타일 상태 접두사
.is-active { }
.is-hidden { }
.is-disabled { }
.is-loading { }
.has-error { }
.has-children { }
```

### 5.3 유틸리티 클래스

```scss
// u- 접두사 사용
.u-hidden { display: none !important; }
.u-text-center { text-align: center !important; }
.u-sr-only {
  position: absolute !important;
  width: 1px !important;
  height: 1px !important;
  overflow: hidden !important;
  clip: rect(0, 0, 0, 0) !important;
}
```

---

## 6. 색상 및 값 규칙

### 6.1 색상

| 규칙 | 상세 |
|---|---|
| 표기 우선순위 | HSL > RGB > Hex |
| Hex 형식 | 소문자, 축약 가능 시 축약 |
| 색상 키워드 금지 | `red`, `blue` 등 CSS 키워드 사용 금지 |
| 시맨틱 변수 | 원시 색상 → 의미적 변수로 매핑 |

```scss
// 1단계: 원시 색상 정의
$blue-500: hsl(210, 100%, 50%);
$gray-900: #1a1a1a;

// 2단계: 시맨틱 매핑
$color-primary: $blue-500;
$color-text: $gray-900;
$color-background: #fff;
$color-border: #ddd;

// 잘못됨 — 색상 키워드
color: red;
background: white;

// 올바름
color: #f00;
background-color: #fff;
```

### 6.2 색상 조작

```scss
// lighten()/darken() 대신 mix() 사용
@function tint($color, $percentage) {
  @return mix(white, $color, $percentage);
}

@function shade($color, $percentage) {
  @return mix(black, $color, $percentage);
}

// 사용
.button:hover {
  background-color: tint($color-primary, 20%);
}
```

---

## 7. 단위 규칙

| 용도 | 권장 단위 | 이유 |
|---|---|---|
| 폰트 크기 | `rem` | 사용자 브라우저 설정 존중, 접근성 |
| 간격 (padding/margin) | `rem` 또는 `em` | 폰트 크기에 비례하여 스케일 |
| 컨테이너 너비 | `%`, `max-width`에 `px` 또는 `rem` | 유동적, 반응형 |
| 테두리 | `px` | 고정, 정밀 |
| 미디어 쿼리 | `em` | 브라우저 간 일관성 |
| 줄 높이 | **단위 없는 숫자** | 복합 효과 방지 |
| 0 값 | **단위 없음** | `margin: 0` (`0px` 아님) |

```scss
// 올바름
html {
  font-size: 100%; // 16px 기본
}

body {
  font-size: 1rem;
  line-height: 1.5; // 단위 없음
}

h1 {
  font-size: 2.5rem;
  margin-bottom: 1rem;
}

.container {
  max-width: 1200px;
  width: 90%;
  padding: 0 1.5rem;
  margin: 0 auto;
}

// 잘못됨 — 폰트에 px 사용 (접근성 문제)
body {
  font-size: 16px;
}
```

### 7.1 SCSS 숫자 처리

```scss
// 단위 추가: 곱셈
$value: 42;
$length: $value * 1px; // 42px

// 단위 제거: 나눗셈
$raw-value: $length / 1px; // 42

// 계산식은 괄호로 감싸기
.element {
  width: (100% / 3);
}
```

---

## 8. SCSS 네스팅 규칙

### 8.1 최대 깊이

**최대 3레벨** (이상적으로 1~2레벨):

```scss
// 올바름 — 2레벨
.block {
  .block__element {
    color: #333;
  }
}

// 잘못됨 — 4레벨 이상
.page {
  .content {
    .article {
      .title {
        color: #333;
      }
    }
  }
}
```

### 8.2 허용되는 네스팅

```scss
.button {
  // 의사 클래스/요소 — 허용
  &:hover { }
  &:focus { }
  &::before { }
  &::after { }

  // 상태 클래스 — 허용
  &.is-active { }
  &.is-disabled { }

  // 컨텍스트 오버라이드 — 허용
  .no-js & { }
}
```

### 8.3 SCSS 규칙셋 내 선언 순서

```scss
.component {
  // 1. 로컬 변수
  $local-var: 10px;

  // 2. @extend 문 (placeholder만)
  @extend %clearfix;

  // 3. @include 문 (@content 없는)
  @include ellipsis;
  @include box-sizing(border-box);

  // 4. 표준 CSS 속성 (유형별 그룹 순서)
  display: block;
  overflow: hidden;
  padding: $local-var;
  color: #333;

  // 5. 중첩 선택자 (앞에 빈 줄)

  &:hover {
    color: #000;
  }

  &::before {
    content: '';
  }

  // 6. @include with @content (미디어 쿼리)
  @include respond-to('medium') {
    overflow: visible;
  }
}
```

---

## 9. SCSS 변수

### 9.1 네이밍

| 유형 | 패턴 | 예시 |
|---|---|---|
| 일반 변수 | `$kebab-case` | `$font-size-base`, `$color-primary` |
| 상수 | `$UPPER_SNAKE_CASE` | `$MAX_WIDTH`, `$CSS_POSITIONS` |
| Private 변수 | `$_kebab-case` | `$_column-width` |
| 플래그/기본값 | `!default` 사용 | `$baseline: 1em !default;` |

### 9.2 변수 생성 기준

변수는 다음 조건을 **모두** 충족할 때 생성:
1. 값이 **최소 2회** 반복됨
2. 값이 **업데이트될 가능성**이 있음
3. 모든 사용처가 **의도적으로 연결**됨

### 9.3 Map 활용

```scss
// 관련 값은 Map으로 관리
$breakpoints: (
  'small': 576px,
  'medium': 768px,
  'large': 992px,
  'xlarge': 1200px,
);

$z-indexes: (
  'below': -1,
  'default': 1,
  'dropdown': 100,
  'sticky': 200,
  'header': 300,
  'overlay': 400,
  'modal': 500,
  'popover': 600,
  'tooltip': 700,
  'toast': 800,
);

// Getter 함수
@function z($layer) {
  @if not map-has-key($z-indexes, $layer) {
    @error 'No z-index found for `#{$layer}`.';
  }
  @return map-get($z-indexes, $layer);
}

// 사용
.modal {
  z-index: z('modal'); // 500
}
```

### 9.4 Map 포매팅

```scss
// 마지막 항목에도 trailing comma
$map: (
  'key1': value1,
  'key2': value2,
  'key3': value3,
);
```

---

## 10. Mixin / Function / Extend

### 10.1 Mixin

**동적 값이나 반복 패턴에 사용:**

```scss
@mixin respond-to($breakpoint) {
  $query: map-get($breakpoints, $breakpoint);
  @if $query {
    @media (min-width: $query) {
      @content;
    }
  } @else {
    @error 'No breakpoint found for `#{$breakpoint}`.';
  }
}

@mixin truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

@mixin size($width, $height: $width) {
  width: $width;
  height: $height;
}
```

### 10.2 Function

**계산된 반환값이 필요할 때 사용:**

```scss
@function em($pixels, $context: 16px) {
  @return ($pixels / $context) * 1em;
}

// 프로젝트 접두사 권장
@function acme-grid-unit($n) {
  @return $n * $acme-column-width;
}
```

### 10.3 @extend

**사용 자제.** 예측 불가한 동작, 미디어 쿼리 경계 불가, 소스 순서 파괴 등의 문제가 있다.

반드시 사용 시 **`%placeholder`만** 사용:

```scss
// 올바름 — placeholder extend
%clearfix {
  &::after {
    content: '';
    display: table;
    clear: both;
  }
}

.container {
  @extend %clearfix;
}

// 잘못됨 — 클래스 extend
.foo {
  @extend .bar;
}
```

---

## 11. 미디어 쿼리

### 11.1 모바일 퍼스트

**`min-width` (모바일 퍼스트)를 기본 접근법으로 사용:**

```scss
// 모바일 기본 스타일
.container {
  padding: 1rem;
}

// 점진적 향상
@media (min-width: 768px) {
  .container {
    padding: 2rem;
  }
}

@media (min-width: 1024px) {
  .container {
    padding: 3rem;
    max-width: 1200px;
  }
}
```

### 11.2 브레이크포인트 네이밍

```scss
// 올바름 — 크기 기반 이름
$breakpoints: (
  'small': 576px,
  'medium': 768px,
  'large': 992px,
  'xlarge': 1200px,
);

// 잘못됨 — 기기 기반 이름
$breakpoints: (
  'ipad': 768px,
  'iphone': 375px,
);
```

### 11.3 인라인 미디어 쿼리

**미디어 쿼리는 컴포넌트와 함께 인라인으로 작성:**

```scss
// 올바름 — 컴포넌트와 함께
.card {
  padding: 1rem;

  @include respond-to('medium') {
    padding: 2rem;
    display: flex;
  }
}

// 비권장 — 파일 하단에 별도 미디어 쿼리 블록
@media (min-width: 768px) {
  .card { padding: 2rem; display: flex; }
  .header { height: 80px; }
}
```

---

## 12. 특이성 (Specificity) 관리

| 규칙 | 상세 |
|---|---|
| ID 선택자 금지 | 스타일링에 `#id` 절대 사용 금지 |
| 불필요한 네스팅 금지 | 선택자 깊이 최소화 |
| 타입 한정 금지 | 클래스에 태그 선택자 추가 금지 |
| 특이성 그래프 | 스타일시트 후반으로 갈수록 특이성이 **점진적으로 증가**해야 함 |

```scss
// 특이성 안전하게 높이기 (필요 시)
.site-nav.site-nav { } // 클래스 이중 사용

// ID를 대상으로 해야 할 때 (서드파티 등)
[id="third-party-widget"] { } // 클래스 수준 특이성

// 제로 특이성 선택자 (Modern CSS)
:where(.card) {
  padding: 1rem; // 특이성: 0,0,0 — 쉽게 오버라이드 가능
}
```

---

## 13. `!important` 규칙

**사전적(proactive) 사용만 허용, 사후적(reactive) 사용 금지:**

```scss
// 올바름 — 유틸리티 클래스 (항상 이겨야 함)
.u-hidden {
  display: none !important;
}

.u-text-center {
  text-align: center !important;
}

// 잘못됨 — 특이성 문제 패치
.header .nav .nav-item .link {
  color: blue;
}
// ... 다른 곳에서:
.link {
  color: red !important; // 금지 — 근본 원인 해결 필요
}
```

---

## 14. Shorthand 속성

### 14.1 의도적 사용

**모든 값을 설정할 때만** shorthand 사용:

```scss
// 올바름 — 모든 면을 의도적으로 설정
padding: 0 1em 2em;
margin: 0 auto;
border: 1px solid #ddd;

// 잘못됨 — 의도하지 않은 속성까지 리셋
.foo {
  background: red; // background-image, background-position 등 리셋됨
}

// 올바름 — 필요한 속성만 설정
.foo {
  background-color: red;
}
```

### 14.2 font shorthand 자제

```scss
// 잘못됨 — font-style, font-variant 등 리셋
.bar {
  font: bold 16px/1.5 sans-serif;
}

// 올바름 — 명시적
.bar {
  font-weight: 700;
  font-size: 1rem;
  line-height: 1.5;
}
```

---

## 15. Z-Index 관리

**중앙 집중식 z-index 스케일 사용** (9장 Map 참조):

| 규칙 | 상세 |
|---|---|
| 임의 값 금지 | `z-index: 9999` 같은 임의 값 금지 |
| Map 사용 | `$z-indexes` Map + `z()` 함수로 관리 |
| 일관된 간격 | 100 단위 증가 |
| 문서화 | 모든 z-index 사용처 문서화 |
| 스태킹 컨텍스트 | z-index는 가장 가까운 스태킹 컨텍스트 기준 — 전역이 아님 |

```scss
// 올바름
.modal {
  z-index: z('modal');
}

// 잘못됨
.modal {
  z-index: 99999;
}
```

---

## 16. 애니메이션 / 트랜지션

### 16.1 성능 안전 속성

**GPU 가속 속성만 애니메이션:**

```scss
// 올바름 — compositor-only 속성
.element {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.element:hover {
  transform: translateY(-4px) scale(1.02);
  opacity: 0.9;
}

// 잘못됨 — 레이아웃 재계산 유발
.element {
  transition: width 0.3s, height 0.3s, top 0.3s, left 0.3s;
}
```

### 16.2 `transition: all` 금지

```scss
// 잘못됨
.element {
  transition: all 0.3s ease;
}

// 올바름 — 속성 명시
.element {
  transition: transform 0.3s ease, opacity 0.3s ease;
}
```

### 16.3 이징 함수

| 용도 | 이징 | 설명 |
|---|---|---|
| 등장 | `ease-out` | 감속 |
| 퇴장 | `ease-in` | 가속 |
| 상태 변화 | `ease-in-out` | 가속 후 감속 |
| UI 요소 | `cubic-bezier(0.4, 0, 0.2, 1)` | Material Design 표준 |
| 금지 | `linear` | UI 요소에 사용 금지 (기계적) |

### 16.4 접근성: 모션 감소 대응

```scss
// 필수: prefers-reduced-motion 대응
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

### 16.5 will-change

```scss
// 애니메이션이 확실히 발생할 요소에만 사용
.element-about-to-animate {
  will-change: transform;
}

// 남용 금지 — 메모리 소비
```

---

## 17. 주석

### 17.1 섹션 주석

```scss
/* ==========================================================================
   섹션 제목
   ========================================================================== */

/* 하위 섹션
   ========================================================================== */
```

또는 CSS Guidelines 스타일:

```scss
/*------------------------------------*\
  #SECTION-TITLE
\*------------------------------------*/
```

### 17.2 SCSS 주석

```scss
// SCSS에서는 라인 주석(//)을 선호 — 컴파일 시 제거됨
// 블록 주석(/* */)은 CSS 출력에 포함됨

// 주석은 대상 위에 별도 줄로 작성
// 줄 끝 주석 자제

// 비명시적 코드에 주석 필수:
// - z-index 값의 이유
// - 브라우저 특정 해결책
// - 매직 넘버
```

### 17.3 문서화 주석 (SassDoc)

```scss
/// 요소의 크기를 설정하는 mixin.
/// @param {Length} $width - 요소 너비
/// @param {Length} $height [$width] - 요소 높이
/// @example scss - 사용법
///   .foo {
///     @include size(10em);
///   }
@mixin size($width, $height: $width) {
  width: $width;
  height: $height;
}
```

### 17.4 번호 주석

```scss
/**
 * 1. 마스트헤드에는 특별한 대비 처리 필요.
 * 2. 부모로부터 상속된 white-space 리셋.
 */
.masthead {
  color: $color-masthead; /* [1] */
  white-space: normal; /* [2] */
}
```

---

## 18. 파일 구조 (7-1 패턴)

```
scss/
|-- abstracts/           # CSS 출력 없음
|   |-- _variables.scss
|   |-- _functions.scss
|   |-- _mixins.scss
|   |-- _placeholders.scss
|
|-- base/                # 리셋, 타이포그래피, 기본 요소 스타일
|   |-- _reset.scss
|   |-- _typography.scss
|   |-- _base.scss
|
|-- components/          # 마이크로 레벨: 버튼, 카드, 모달
|   |-- _button.scss
|   |-- _card.scss
|   |-- _dropdown.scss
|   |-- _modal.scss
|
|-- layout/              # 매크로 레벨: 헤더, 푸터, 그리드
|   |-- _header.scss
|   |-- _footer.scss
|   |-- _sidebar.scss
|   |-- _grid.scss
|
|-- pages/               # 페이지별 오버라이드
|   |-- _home.scss
|   |-- _contact.scss
|
|-- themes/              # 테마 변형
|   |-- _default.scss
|   |-- _dark.scss
|
|-- vendors/             # 서드파티 CSS
|   |-- _normalize.scss
|
|-- main.scss            # 단일 진입점, import만
```

### 18.1 main.scss Import 순서

```scss
// abstracts (출력 없음)
@import 'abstracts/variables';
@import 'abstracts/functions';
@import 'abstracts/mixins';

// vendors
@import 'vendors/normalize';

// base
@import 'base/reset';
@import 'base/typography';

// layout
@import 'layout/grid';
@import 'layout/header';
@import 'layout/footer';

// components
@import 'components/button';
@import 'components/card';
@import 'components/modal';

// pages
@import 'pages/home';

// themes
@import 'themes/default';
```

---

## 19. 금지 패턴

| 패턴 | 이유 | 대안 |
|---|---|---|
| `#id` 선택자 | 특이성 과도 | 클래스 선택자 사용 |
| `!important` (사후적) | 특이성 전쟁 유발 | 선택자 구조 개선 |
| `transition: all` | 성능 저하, 의도하지 않은 속성 전환 | 개별 속성 명시 |
| `@extend .class` | 예측 불가, 소스 순서 파괴 | mixin 사용 또는 `%placeholder` |
| 색상 키워드 | 비표준적, 불명확 | hex / hsl / rgb |
| `z-index: 9999` | 관리 불가 | Map + getter 함수 |
| 4레벨 이상 네스팅 | 특이성 증가, 가독성 저하 | BEM으로 평탄화 |
| `font-size: px` | 접근성 문제 | `rem` 사용 |
| `div.class` | 불필요한 특이성, 재사용성 저하 | `.class`만 사용 |
| `*` 남용 | 성능 저하 | 명시적 선택자 |
| 매직 넘버 | 유지보수 불가 | 변수 + 주석 |
| `width`/`height` 애니메이션 | 레이아웃 재계산 | `transform` 사용 |
| `.5em` (앞자리 0 생략) | 가독성 저하 | `0.5em` |

---

## 20. Stylelint 권장 설정

```json
{
  "extends": ["stylelint-config-standard-scss"],
  "plugins": ["stylelint-order"],
  "rules": {
    "color-no-invalid-hex": true,
    "declaration-block-no-duplicate-properties": true,
    "no-duplicate-selectors": true,
    "block-no-empty": true,
    "color-hex-length": "short",
    "max-nesting-depth": 3,
    "selector-max-specificity": "0,3,0",
    "selector-max-id": 0,
    "number-max-precision": 4,
    "property-no-vendor-prefix": true,
    "selector-no-vendor-prefix": true,
    "value-no-vendor-prefix": true,
    "no-descending-specificity": true,
    "declaration-no-important": true,
    "selector-no-qualifying-type": true,
    "order/properties-order": [
      "position", "top", "right", "bottom", "left", "z-index",
      "display", "flex-direction", "flex-wrap", "justify-content", "align-items",
      "overflow", "box-sizing", "width", "height", "padding", "border", "margin",
      "font-family", "font-size", "font-weight", "line-height", "color", "text-align",
      "background", "background-color", "border-radius", "box-shadow", "opacity",
      "transition", "animation", "cursor"
    ]
  }
}
```

---

## 핵심 수치 요약

| 카테고리 | 규칙 | 값 |
|---|---|---|
| 들여쓰기 | 스페이스 크기 | 2 |
| 줄 길이 | 최대 문자 수 | 80 |
| 네스팅 | 최대 깊이 | 3 |
| 특이성 | 최대 허용 | 0,3,0 |
| ID 선택자 | 최대 개수 | 0 |
| 소수점 | 최대 자릿수 | 4 |
| z-index | 기본 간격 | 100 |
| 색상 Hex | 형식 | 소문자, 축약 |
| 0 값 | 단위 | 없음 |
| 앞자리 0 | 표기 | 필수 (`0.5`) |
| 폰트 크기 | 단위 | rem |
| 줄 높이 | 단위 | 없음 (숫자만) |
| 네이밍 | 패턴 | BEM (kebab-case) |
| 파일명 | 패턴 | kebab-case |
| 변수 | 패턴 | $kebab-case |
| 상수 | 패턴 | $UPPER_SNAKE_CASE |

---

## 참고 자료

- [CSS Guidelines - Harry Roberts](https://cssguidelin.es/)
- [Sass Guidelines - Kitty Giraudel](https://sass-guidelin.es/)
- [Google HTML/CSS Style Guide](https://google.github.io/styleguide/htmlcssguide.html)
- [Airbnb CSS/Sass Styleguide](https://github.com/airbnb/css)
- [BEM Official](https://getbem.com/naming/)
- [SMACSS - Jonathan Snook](https://smacss.com/)
- [ITCSS - Harry Roberts](https://www.xfive.co/blog/itcss-scalable-maintainable-css-architecture/)
- [Idiomatic CSS - Nicolas Gallagher](https://github.com/necolas/idiomatic-css)
- [Stylelint](https://stylelint.io/)
- [stylelint-config-standard-scss](https://github.com/stylelint-scss/stylelint-config-standard-scss)
