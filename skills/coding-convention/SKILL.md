---
name: coding-convention
description: >
  코드를 작성하거나 수정할 때 프로젝트의 코딩 컨벤션을 자동 적용합니다.
  Java, TypeScript, Python, CSS/SCSS 파일을 편집하거나 새로 생성할 때,
  코드 리뷰나 리팩토링을 수행할 때 사용합니다.
---

# 코딩 컨벤션 적용 스킬

코드를 작성하거나 수정할 때 반드시 아래 컨벤션을 따르라.
각 언어의 상세 규칙은 이 디렉토리의 참조 문서를 확인하라.

## 언어 판별

작업 대상 파일의 확장자로 언어를 판별한다:
- `.java` → Java 컨벤션 적용 (참조: java-coding-convention.md)
- `.ts`, `.tsx` → TypeScript 컨벤션 적용 (참조: typescript-coding-convention.md)
- `.py` → Python 컨벤션 적용 (참조: python-coding-convention.md)
- `.css`, `.scss`, `.sass` → CSS/SCSS 컨벤션 적용 (참조: css-scss-coding-convention.md)

## Java 컨벤션 핵심 (Google Java Style Guide 기반)

- **인코딩**: UTF-8
- **인덴트**: 2스페이스(블록), +4스페이스(연속줄)
- **줄 길이**: 100자
- **중괄호**: K&R 스타일, 단일 문장도 항상 사용
- **네이밍**:
  - 패키지: `com.example.deepspace` (전소문자)
  - 클래스: `UpperCamelCase` (테스트 클래스는 `Test`로 끝남)
  - 메서드: `lowerCamelCase` (동사/동사구)
  - 상수: `UPPER_SNAKE_CASE` (`static final` + 깊이 불변)
  - 필드/파라미터/지역변수: `lowerCamelCase`
  - 타입 변수: `T`, `E`, `RequestT`
- **Import**: 와일드카드 금지, static/non-static 분리, ASCII 정렬
- **Javadoc**: 모든 `public`/`protected` 멤버에 필수. 요약은 명사/동사구(문장 아님)
- **@Override**: 합법적 시 항상 사용
- **예외**: 절대 조용히 무시 금지
- **정적 멤버**: 클래스 이름으로만 접근 (`인스턴스.staticMethod()` 금지)
- **finalize()**: 오버라이드 금지
- **배열**: `String[] args` (O), `String args[]` (X)
- **long 리터럴**: 대문자 `L` (`100L`)

## CSS/SCSS 컨벤션 핵심 (CSS Guidelines + Sass Guidelines 기반)

- **인덴트**: 2스페이스, 탭 금지
- **줄 길이**: 80자
- **네이밍**: BEM (`block__element--modifier`), 소문자 kebab-case
- **파일명**: `kebab-case`, SCSS 파셜은 `_` 접두사
- **선택자**:
  - ID 선택자 금지 (`#id` 사용 금지)
  - 타입 한정 금지 (`div.class` 금지)
  - 최대 깊이 3레벨 (이상적으로 1~2)
- **속성 순서**: 유형별 그룹 (Positioning → Box Model → Typography → Visual → Animation → Misc)
- **색상**: 소문자 hex, 축약 가능 시 축약 (`#fff`), 색상 키워드 금지
- **단위**: 폰트 `rem`, 줄 높이 단위 없는 숫자, 0에 단위 금지
- **값 표기**: 앞자리 0 필수 (`0.5`), `border: 0` (`none` 아님)
- **SCSS 네스팅**: 최대 3레벨, 의사 클래스/상태 클래스만 네스팅 권장
- **SCSS 변수**: `$kebab-case`, 상수 `$UPPER_SNAKE_CASE`, 관련 값은 Map 관리
- **@extend**: 사용 자제, 필수 시 `%placeholder`만
- **!important**: 유틸리티 클래스에만 사전적 사용, 사후적 사용 금지
- **미디어 쿼리**: 모바일 퍼스트 (`min-width`), 컴포넌트와 인라인 작성
- **z-index**: Map + getter 함수 중앙 관리, 임의 값 금지
- **애니메이션**: `transform`/`opacity`만, `transition: all` 금지, `prefers-reduced-motion` 대응 필수
- **파일 구조**: 7-1 패턴 (abstracts, base, components, layout, pages, themes, vendors)
- **금지 패턴**: ID 선택자, `!important` 남용, `transition: all`, `@extend .class`, 색상 키워드, 매직 넘버, 4레벨+ 네스팅

## TypeScript 컨벤션 핵심 (Google TypeScript Style Guide 기반)

- **세미콜론**: 항상 필수
- **포매터**: Prettier
- **네이밍**:
  - 클래스/인터페이스/타입/enum: `UpperCamelCase`
  - 변수/파라미터/함수/메서드: `lowerCamelCase`
  - 전역 상수, enum 값: `CONSTANT_CASE`
  - 파일명: `snake_case`
  - 타입 파라미터: `T` 또는 `UpperCamelCase`
- **Export**: `export default` 금지, named export만 사용
- **Import**: `import type {...}` 사용, `require()` 금지
- **타입**: `any` 금지 → `unknown` 사용. 객체는 `interface`, 유니온/튜플은 `type alias`
- **변수**: `const` 기본, 재할당 시에만 `let`. `var` 절대 금지
- **금지 패턴**: `var`, `const enum`, `export default`, `export let`, `namespace`, `#ident`, `.forEach()`, `.bind()/.call()/.apply()`, `@ts-ignore`, `@ts-nocheck`
- **제어 흐름**: `===`/`!==` 필수 (예외: `== null`). `for...of` 사용 (배열에 `for...in` 금지)
- **에러 처리**: `new Error()` 필수, catch는 `unknown`, 빈 catch 정당화 필요

## Python 컨벤션 핵심 (PEP 8 기반)

- **인덴트**: 4스페이스, 탭 금지
- **줄 길이**: 79자 (주석/독스트링 72자)
- **네이밍**:
  - 패키지: `mypackage` (짧은 소문자)
  - 모듈: `my_module` (소문자_언더스코어)
  - 클래스: `CapWords` (CamelCase)
  - 예외: `CapWords` + `Error` 접미사
  - 함수/메서드: `lowercase_with_underscores`
  - 상수: `UPPER_CASE_WITH_UNDERSCORES`
  - 인스턴스 메서드 첫 인자: `self`, 클래스 메서드: `cls`
- **Import 순서**: 표준 라이브러리 → 서드파티 → 로컬 (그룹 사이 빈 줄)
- **와일드카드 import 금지**: `from module import *` 회피
- **비교**: `is None` / `is not None` (`== None` 금지), `isinstance()` 사용 (`type()` 금지)
- **빈 시퀀스**: `if not seq:` (falsy 활용), `if len(seq):` 금지
- **독스트링**: `"""삼중 큰따옴표"""`, 명령형 ("Return X"), 모든 공개 멤버
- **Lambda 할당 금지**: `f = lambda x: 2*x` 금지 → `def f(x): return 2*x`
- **리소스 관리**: `with` 문 사용
- **빈 줄**: 최상위 정의 전후 2줄, 클래스 내 메서드 전후 1줄
