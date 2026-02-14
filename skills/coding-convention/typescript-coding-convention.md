# TypeScript Coding Convention

> **Google TypeScript Style Guide** 기반
>
> - 공식 문서: https://google.github.io/styleguide/tsguide.html
> - 커뮤니티 에디션: https://ts.dev/style/
> - 도구: https://github.com/google/gts
> - 관리: Google Engineering

---

## 1. 선정 사유

| 기준 | Google TS Guide | Airbnb | Microsoft | StandardJS |
|---|---|---|---|---|
| TypeScript 네이티브 | O | X (JS 기반) | O (내부용) | 부분적 |
| 포괄성 | 매우 높음 | JS는 높으나 TS 부족 | 범위 좁음 | 최소 |
| 활발한 유지보수 | O (gts v7.0.0, 2025.12) | X (2023.01 이후 중단) | 산발적 | 낮음 |
| 도구 지원 | gts (ESLint + Prettier) | 커뮤니티 유지 | 없음 | ts-standard |
| 타입 시스템 커버리지 | 광범위 | 없음 | 최소 | 없음 |

Google TypeScript Style Guide는 TypeScript 전용으로 설계되었으며, 타입 시스템, 모듈, 클래스, 에러 처리 등 모든 영역을 포괄하는 유일한 주요 가이드이다.

---

## 2. 소스 파일 기본 규칙

| 규칙 | 상세 |
|---|---|
| 인코딩 | UTF-8 필수 |
| 공백 문자 | ASCII 수평 공백(0x20)만 허용 |
| 이스케이프 시퀀스 | `\'`, `\"`, `\\`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v` 사용. 숫자 이스케이프 금지 |
| 8진수 이스케이프 | 금지 (레거시) |
| Non-ASCII 문자 | 실제 유니코드 문자 사용 권장. 비인쇄 문자는 주석 첨부 |
| 포매터 | Prettier 사용. 수동 포매팅 논쟁 금지 |
| 세미콜론 | 항상 필수. ASI(자동 세미콜론 삽입)에 의존 금지 |

---

## 3. 소스 파일 구조

파일은 다음 순서로 구성하며, 각 섹션 사이에 **정확히 한 줄**의 빈 줄을 삽입한다:

1. 저작권 정보 (있는 경우)
2. `@fileoverview` JSDoc (있는 경우)
3. Imports
4. 파일 구현부

---

## 4. Import / Export 규칙

### 4.1 Import

| 규칙 | 상세 |
|---|---|
| 허용되는 Import 유형 | 모듈(`import * as foo`), Named(`import {Thing}`), Default(외부 라이브러리 필수 시), Side-effect(`import '...'`) |
| Default import | 비권장. 외부 라이브러리가 요구할 때만 사용 |
| Import 경로 | 동일 프로젝트는 상대 경로(`./foo`) 사용. 상위 탐색(`../../../`) 제한 |
| Namespace vs Named | 자주 사용하는 심볼은 Named, 대형 API의 다수 심볼은 Namespace |
| Import 이름 변경 | 충돌 해소, 생성된 이름, 명확성 향상 시 허용 |
| `import type` | 타입 전용 import에 `import type {...}` 사용 (Google 버전) |
| `require()` | 금지 |
| `/// <reference>` | 금지 |

### 4.2 Export

| 규칙 | 상세 |
|---|---|
| Named export만 사용 | `export class Foo { }`. `export default` 금지 |
| Default export 금지 이유 | 정식 이름 없음, 존재하지 않는 심볼 import 시 에러 미발생, 일관성 저하 |
| API 최소화 | 모듈 외부에서 실제 사용하는 심볼만 export |
| 가변 export 금지 | `export let` 금지. 가변 바인딩은 명시적 getter 함수 사용 |
| 컨테이너 클래스 금지 | 정적 메서드/프로퍼티만 가진 네임스페이스용 클래스 금지. 개별 상수/함수로 export |

### 4.3 모듈 시스템

| 규칙 | 상세 |
|---|---|
| ES6 모듈만 | `import`/`export` 문 사용 필수 |
| `namespace` 금지 | `namespace Foo { }` 사용 금지 |
| 코드 조직 | 타입별(views/models)이 아닌 기능별(products/checkout) 구성 |

---

## 5. 네이밍 규칙

### 5.1 식별자 스타일

| 스타일 | 적용 대상 |
|---|---|
| `UpperCamelCase` | 클래스, 인터페이스, 타입, enum, 데코레이터, 타입 파라미터, JSX 컴포넌트 |
| `lowerCamelCase` | 변수, 파라미터, 함수, 메서드, 프로퍼티, 모듈 별칭 |
| `CONSTANT_CASE` | 전역 상수, `static readonly` 클래스 프로퍼티, enum 값 |

### 5.2 세부 규칙

| 규칙 | 상세 |
|---|---|
| 타입 정보를 이름에 포함하지 않음 | 타입 시스템이 이미 표현 |
| 언더스코어 접두/접미사 금지 | private 멤버, 미사용 변수 등에 `_` 사용 금지 |
| 인터페이스 `I` 접두사 금지 | `IMyInterface` 사용 금지 |
| `opt_` 접두사 금지 | 선택적 파라미터에 `opt_` 사용 금지 |
| 약어는 단어로 취급 | `loadHttpUrl` (O), `loadHTTPURL` (X). 예외: `XMLHttpRequest` 등 플랫폼 이름 |
| 달러 기호(`$`) | 서드파티 프레임워크(jQuery, RxJS) 요구 시에만 사용 |
| 서술적 이름 | 새 독자가 이해할 수 있는 명확한 이름 |
| 짧은 이름 | 단일 문자 변수는 10줄 이하 범위에서만 허용 |
| 타입 파라미터 | 단일 대문자(`T`) 또는 `UpperCamelCase` |
| 테스트 메서드 | xUnit 스타일 `_` 구분 허용: `testX_whenY_doesZ()` |
| 파일명 | `snake_case` |
| 상수 | 깊이 불변인 값만 `CONSTANT_CASE`. 여러 번 인스턴스화되면 `lowerCamelCase` |

---

## 6. 타입 시스템

### 6.1 타입 추론

| 규칙 | 상세 |
|---|---|
| 추론 활용 | 명백한 타입(string, number, boolean, RegExp, `new` 표현식)은 어노테이션 생략 |
| 도움이 될 때 명시 | 복잡한 표현식에서 추론이 불분명할 때 어노테이션 사용 |
| 반환 타입 | 선택 사항. 리뷰어가 명확성을 위해 요청 가능 |

### 6.2 Null과 Undefined

| 규칙 | 상세 |
|---|---|
| 일반적 선호 없음 | 컨텍스트에 따라 결정 |
| Nullable 타입 별칭 | 타입 별칭에 `\|null`, `\|undefined` 포함 금지. 사용 시점에서만 추가 |
| Optional vs undefined | 필드/파라미터에서 `\|undefined`보다 `field?` 선호 |
| Null 비교 | `== null`로 null과 undefined 동시 체크 허용 |

### 6.3 Interface vs Type Alias

| 규칙 | 상세 |
|---|---|
| 객체 타입 | `interface` 사용 (type alias + 객체 리터럴 금지) |
| 유니온, 튜플, 프리미티브 | type alias 사용 |
| 이유 | interface가 더 나은 표시, 성능, 의도 명확성 제공 |

### 6.4 배열 타입

| 규칙 | 상세 |
|---|---|
| 단순 타입 | `T[]` 또는 `readonly T[]` 사용 |
| 복잡한 타입 | `Array<string \| number>` 사용 |
| 다차원 | `T[][]`, `T[][][]` |

### 6.5 `any` 타입

| 규칙 | 상세 |
|---|---|
| `any` 회피 | 사용 금지. 구체적 타입, `unknown`, 또는 린트 억제 + 문서화 고려 |
| `unknown` 우선 | `unknown`이 더 안전 - 사용 전 타입 좁히기 필요 |
| 경고 억제 | `any`가 정당한 이유(테스트 mock 등) 설명 주석 추가 |

### 6.6 `{}` (빈 객체 타입)

`{}` 대신 `unknown`(null/undefined 포함 모든 값), `Record<string, T>`(딕셔너리), `object`(프리미티브 제외) 사용.

### 6.7 Enum

| 규칙 | 상세 |
|---|---|
| `enum` 사용 | 항상 `enum` 사용, `const enum` 금지 |
| Enum 값 | `CONSTANT_CASE` |
| Boolean 변환 | `Boolean()`이나 `!!`로 변환 금지. 비교 연산자로 명시적 비교 |

### 6.8 래퍼 타입

`String`, `Boolean`, `Number` 사용 금지. 항상 소문자 `string`, `boolean`, `number` 사용. `new String()` 등 생성자 사용 금지.

---

## 7. 변수

| 규칙 | 상세 |
|---|---|
| `const` 기본 | 항상 `const` 사용. 재할당 시에만 `let` |
| `let` | 재할당되는 변수에만 사용 |
| `var` 금지 | 절대 사용 금지 |
| 선언 당 하나 | `let a = 1, b = 2;` 금지 |
| 전방 참조 금지 | 선언 전 변수 사용 금지 |

---

## 8. 함수

### 8.1 선언과 표현식

| 규칙 | 상세 |
|---|---|
| 함수 선언 선호 | 명명된 함수에는 `function foo() { }` 사용 |
| 화살표 함수 | ES6 이전 `function` 키워드 대신 표현식에 사용 |
| 함수 표현식 금지 | 화살표 함수 사용. 예외: 동적 `this` 바인딩, 제너레이터 함수 |

### 8.2 화살표 함수 본문

| 규칙 | 상세 |
|---|---|
| 간결한 본문 | 반환값이 실제 사용될 때만 |
| 블록 본문 | 반환값이 미사용일 때 (실수로 반환 방지) |

### 8.3 `this` 키워드

| 규칙 | 상세 |
|---|---|
| 사용 가능 | 클래스 생성자, 클래스 메서드, 명시적 `this` 타입 함수, 유효 `this` 범위의 화살표 함수 |
| 사용 금지 | 전역 객체, 이벤트 타겟 |
| 선호 | `.bind()`, `.call()`, `.apply()` 대신 화살표 함수 또는 명시적 파라미터 |

### 8.4 파라미터

| 규칙 | 상세 |
|---|---|
| 기본값 초기화 | 부작용 없는 간단한 초기화 사용 |
| 구조 분해 | 다수의 선택적 파라미터 시 가독성 향상을 위해 사용 |
| Rest 파라미터 | `arguments` 대신 사용. `arguments`라는 지역 변수명 금지 |
| 스프레드 | `Function.prototype.apply` 대신 함수 스프레드 사용 |

### 8.5 생성자

| 규칙 | 상세 |
|---|---|
| 항상 괄호 | `new Foo()` (O), `new Foo` (X) |
| 빈 생성자 생략 | 파라미터 프로퍼티, 가시성 수정자, 데코레이터 없으면 생략 |

---

## 9. 클래스

### 9.1 멤버 규칙

| 규칙 | 상세 |
|---|---|
| Private field 문법 금지 | `#ident` 금지. TypeScript `private` 키워드 사용 |
| `readonly` | 생성자 외부에서 재할당되지 않는 프로퍼티에 적용 |
| 파라미터 프로퍼티 | 보일러플레이트 제거: `constructor(private readonly barService: BarService) {}` |
| 선언 시 초기화 | 생성자 초기화보다 필드 이니셜라이저 선호 |
| 형태 변경 금지 | 생성자 완료 후 프로퍼티 추가/제거 금지 |
| 가시성 제한 | 최대한 제한. `public` 수정자는 비읽기전용 파라미터 프로퍼티에만 사용 |
| 괄호 접근 금지 | 가시성 우회를 위한 `obj['foo']` 금지 |

### 9.2 Getter와 Setter

| 규칙 | 상세 |
|---|---|
| Getter는 순수해야 | 일관된 결과, 부작용 없음, 관찰 가능한 상태 변경 없음 |
| 비자명한 로직 필수 | 프로퍼티당 최소 하나의 접근자에 로직 필요 (단순 필드 래핑 금지) |

### 9.3 정적 메서드

| 규칙 | 상세 |
|---|---|
| Private static 회피 | 모듈-로컬(비export) 함수 선호 |
| 동적 디스패치 금지 | 정적 메서드는 기본 클래스에서만 호출 |
| Static에서 `this` 금지 | 정적 컨텍스트에서 `this` 사용 금지 |

---

## 10. 제어 흐름

| 규칙 | 상세 |
|---|---|
| 중괄호 사용 | 다중 줄 블록에 항상 사용. 단일 줄 `if`는 생략 가능: `if (x) x.doFoo();` |
| 제어문 내 할당 | 회피. 사용 시 추가 괄호로 의도 표시 |
| 배열 순회 | `for (... of someArr)` 사용. 배열에 `for...in` 금지 (문자열 인덱스 반환) |
| 객체 순회 | `Object.keys(obj)` 또는 `Object.entries()` 사용 |
| `.forEach()` | 금지 (컴파일러 도달성 검사 무효화) |
| `===` / `!==` | 항상 사용. 예외: `== null`로 null/undefined 동시 체크 |
| Switch `default` | 항상 존재해야 하며 마지막에 위치 |
| Switch fall-through | 빈 그룹만 fall-through 허용. 비어있지 않은 그룹은 금지 |

---

## 11. 에러 처리

| 규칙 | 상세 |
|---|---|
| 항상 `new Error()` | `Error()` without `new` 금지 |
| Error 인스턴스만 throw | 또는 서브클래스. 커스텀 예외 선호 |
| Catch에서 `unknown` | catch 파라미터를 `unknown`으로 선언. 타입 가드로 좁히기 |
| 빈 catch 블록 | 주석으로 정당화하지 않으면 금지 |
| try 블록 범위 제한 | throw하지 않는 코드는 try-catch 외부로 이동 |
| Promise rejection | `Promise.reject(new Error(...))` 사용. 비 Error 값으로 reject 금지 |

---

## 12. 타입 강제 변환

| 규칙 | 상세 |
|---|---|
| 문자열 변환 | `String()` 함수 또는 템플릿 리터럴 사용. 문자열 연결로 캐스팅 금지 |
| Boolean 변환 | `Boolean()` 또는 `!!` 사용. 암시적 강제 변환하는 조건에서 명시적 변환 불필요 |
| 숫자 변환 | `Number()` 사용. 반환값의 `NaN` 명시적 체크 필수 |
| 단항 `+` 금지 | `+x`로 문자열->숫자 변환 금지 |
| `parseInt`/`parseFloat` | 10진수 이외의 문자열에만 사용. 후행 문자를 조용히 무시하므로 주의 |
| 정수 파싱 | `Number()` 후 `Math.floor()` 또는 `Math.trunc()` |

---

## 13. 주석과 문서화

### 13.1 JSDoc vs 라인 주석

| 규칙 | 상세 |
|---|---|
| `/** JSDoc */` | 문서화용 (도구, 에디터, 생성기가 소비) |
| `// 라인 주석` | 구현 상세용 (사람 독자만) |
| 다중 줄 주석 | 여러 `//` 줄 사용. `/* */` 블록 스타일 금지 |
| 장식 상자 금지 | 별표 프레임으로 감싸지 않음 |

### 13.2 JSDoc 내용 규칙

| 규칙 | 상세 |
|---|---|
| 모든 최상위 export 문서화 | 모듈 소비자를 위한 목적 설명 |
| 타입 재진술 금지 | TypeScript가 이미 선언. `@type`, `@implements`, `@enum`, `@private` 생략 |
| `@override` 사용 금지 | 컴파일러가 강제하지 않으므로 어노테이션 불일치 발생 |
| `@param`/`@return` | 시그니처 이상의 정보가 있을 때만 사용 |

### 13.3 허용되는 JSDoc 태그

`@fileoverview`, `@param`, `@return`, `@throws`, `@deprecated`, `@see`, `@example`, `@nocollapse`

---

## 14. 데코레이터

| 규칙 | 상세 |
|---|---|
| 새 데코레이터 정의 금지 | 프레임워크 제공 데코레이터만 사용 (Angular, Polymer 등) |
| 이유 | 실험적 기능, 알려진 버그, TC39 표준과의 차이 |
| 배치 | 장식 대상 심볼 바로 앞. 사이에 빈 줄 금지 |

---

## 15. 금지 기능 목록

| # | 금지 기능 |
|---|---|
| 1 | `var` 키워드 |
| 2 | `const enum` |
| 3 | `export default` |
| 4 | `export let` (가변 export) |
| 5 | `namespace Foo { }` |
| 6 | `require()` imports |
| 7 | `/// <reference>` 지시문 |
| 8 | 래퍼 타입 생성자 (`new String()`, `new Boolean()`, `new Number()`) |
| 9 | `Array()` 생성자 |
| 10 | `Object()` 생성자 |
| 11 | Private field 문법 (`#ident`) |
| 12 | 동적 코드 실행 함수 및 `Function(...string)` 생성자 |
| 13 | `with` 문 |
| 14 | `debugger` 문 (프로덕션) |
| 15 | 내장 프로토타입 수정 |
| 16 | 전역 객체에 심볼 추가 |
| 17 | 비표준 ECMAScript 기능 |
| 18 | 레거시 8진수 이스케이프 |
| 19 | `@ts-ignore` 지시문 |
| 20 | `@ts-nocheck` 지시문 |
| 21 | `@ts-expect-error` (테스트 제외) |
| 22 | 함수 표현식 (화살표 함수 사용) |
| 23 | 컨테이너/네임스페이스 클래스 (정적 전용 클래스) |
| 24 | `.forEach()` (배열, Map, Set) |

---

## 16. 일관성과 모호성

| 규칙 | 상세 |
|---|---|
| 로컬 일관성 | 가이드에서 정하지 않은 문제는 동일 파일 패턴 > 동일 디렉토리 패턴 순으로 따름 |
| 포매터 권위 | 소스 코드 포매팅은 Prettier로 자동화. 수동 포매팅 논쟁 금지 |

---

## 참고 자료

- [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html)
- [ts.dev Style Guide (Community Edition)](https://ts.dev/style/)
- [Google gts - GitHub](https://github.com/google/gts)
- [Airbnb JavaScript Style Guide - GitHub](https://github.com/airbnb/javascript)
- [Microsoft TypeScript Coding Guidelines](https://github.com/microsoft/TypeScript/wiki/Coding-guidelines)
