# Java Coding Convention

> **Google Java Style Guide** 기반
>
> - 공식 문서: https://google.github.io/styleguide/javaguide.html
> - 도구: https://github.com/google/google-java-format
> - 관리: Google Engineering
> - GitHub Stars: ~38,900+ (google/styleguide 리포지토리)

---

## 1. 선정 사유

| 기준 | Google Java | Oracle/Sun | Alibaba | Spring |
|---|---|---|---|---|
| 커뮤니티 채택 | 전 세계 가장 널리 채택 | 역사적 기반 (레거시) | 중국 생태계 중심 | Spring 기여 전용 |
| 최신성 | 활발 유지보수 (records, sealed classes 등) | 1999년 이후 미갱신 | 활발 | Spring 전용 |
| 포괄성 | 파일 구조, 포매팅, 네이밍, 프로그래밍 관행, Javadoc | 기본적 | 매우 넓음 (DB/보안 포함) | 좁음 |
| 도구 지원 | google-java-format, Checkstyle, IDE 설정 | 없음 | PMD 플러그인, IDE 플러그인 | 없음 |
| 명확성 | 처방적, 모호성 최소 | 일부 모호 | 높음 | 기본적 |

Google Java Style Guide는 현대 Java 기능을 포괄하며, 전 세계적으로 가장 널리 채택된 Java 스타일 가이드이다. Spring Framework의 코드 스타일도 Google Java Style을 기반으로 한다.

---

## 2. 소스 파일 기본 규칙

### 2.1 파일명

- 최상위 클래스의 대소문자 구분 이름 + `.java`
- 파일당 정확히 하나의 최상위 클래스

### 2.2 파일 인코딩

- UTF-8 필수

### 2.3 특수 문자

| 규칙 | 상세 |
|---|---|
| 공백 문자 | ASCII 수평 공백(0x20)만 허용. **탭 문자 금지** |
| 이스케이프 시퀀스 | `\b`, `\t`, `\n`, `\f`, `\r`, `\s`, `\"`, `\'`, `\\` 사용. 8진수/유니코드 이스케이프 대신 사용 |
| Non-ASCII 문자 | 실제 유니코드 문자 또는 유니코드 이스케이프 중 **가독성 높은 쪽** 사용. 유니코드 이스케이프 시 설명 주석 첨부 |

---

## 3. 소스 파일 구조

파일은 다음 순서로 구성하며, 각 섹션 사이에 **정확히 한 줄**의 빈 줄을 삽입:

1. 라이선스/저작권 정보 (있는 경우)
2. Package 문 (줄 바꿈 금지, 칼럼 제한 미적용)
3. Import 문
4. 정확히 하나의 최상위 클래스

---

## 4. Import 규칙

| 규칙 | 상세 |
|---|---|
| 와일드카드 import 금지 | `import java.util.*` 금지 (정적/비정적 모두) |
| 줄 바꿈 금지 | import 문은 줄 바꿈하지 않음, 칼럼 제한 미적용 |
| 순서 | 모든 정적 import를 하나의 블록, 모든 비정적 import를 하나의 블록 |
| 블록 간 구분 | 정적/비정적 블록 사이에 빈 줄 하나 |
| 블록 내 정렬 | ASCII 정렬 순서 |
| 정적 중첩 클래스 | 일반(비정적) import 사용 |

---

## 5. 클래스 선언

| 규칙 | 상세 |
|---|---|
| 파일당 하나 | 정확히 하나의 최상위 클래스 |
| 멤버 순서 | **논리적 순서** (유지관리자가 설명 가능한). 추가 시간순 아님 |
| 오버로드 분리 금지 | 같은 이름의 여러 생성자/메서드는 순차 배치, 사이에 다른 코드 없음 |

---

## 6. 포매팅

### 6.1 중괄호

**필수 중괄호**: `if`, `else`, `for`, `do`, `while` 문에서 본문이 비어있거나 단일 문장이어도 중괄호 사용. 예외: 람다 표현식.

**비어있지 않은 블록 (K&R 스타일)**:
- 여는 중괄호 앞에 줄 바꿈 없음
- 여는 중괄호 뒤에 줄 바꿈
- 닫는 중괄호 앞에 줄 바꿈
- 닫는 중괄호 뒤에 줄 바꿈 (문장/메서드/생성자/클래스 본문 종료 시만). `else`, `catch`, 쉼표 앞에서는 줄 바꿈 없음

**빈 블록**: 간결한 `{}` 허용 (내부 문자/줄 바꿈 없음). 예외: 다중 블록 문(`if/else`, `try/catch/finally`)에서는 불허.

### 6.2 블록 들여쓰기

- 새 블록마다 **+2칸 공백**
- 블록 내 코드와 주석 모두 적용

### 6.3 한 줄에 하나의 문장

각 문장 뒤에 줄 바꿈.

### 6.4 칼럼 제한

- **100자** per line
- 각 유니코드 코드 포인트가 하나의 문자로 카운트

**예외** (100자 초과 허용):
- 칼럼 제한 준수 불가능한 줄 (예: Javadoc의 긴 URL)
- Package 및 import 문
- 텍스트 블록 내용
- 셸에 복사할 수 있는 주석 내 명령줄
- 매우 긴 식별자 (드묾)

### 6.5 줄 바꿈

**핵심 원칙**: **더 높은 구문 수준**에서 줄 바꿈 선호.

| 컨텍스트 | 줄 바꿈 위치 |
|---|---|
| 비할당 연산자 (`.`, `::`, `&`, `\|`) | 연산자 **앞** |
| 할당 연산자 (향상된 `for`의 콜론 포함) | 연산자 **뒤** (양방향 허용) |
| 메서드/생성자/레코드 이름 | 여는 `(`에 붙임 |
| 쉼표 | 앞 토큰에 붙임 |
| 람다 화살표 (`->`) | 인접 줄 바꿈 금지 |
| Switch 규칙 화살표 (`->`) | 인접 줄 바꿈 금지 |

**연속 들여쓰기**: 원래 줄에서 최소 **+4칸 공백**.

### 6.6 공백

#### 수직 공백 (빈 줄)

- 클래스의 연속 멤버/이니셜라이저 사이에 단일 빈 줄
- 연속 필드 사이 빈 줄은 선택 (논리적 그룹화용)
- 가독성 향상을 위해 어디서든 추가 빈 줄 허용
- 연속 빈 줄 여러 개 허용이나 권장하지 않음

#### 수평 공백 (단일 공백)

단일 ASCII 공백이 나타나는 **유일한** 위치:

1. 예약어(`if`, `for`, `catch` 등)와 뒤따르는 `(` 사이
2. 예약어(`else`, `catch`)와 앞의 `}` 사이
3. 모든 여는 중괄호 `{` 앞 (예외: `@SomeAnnotation({a, b})`, `String[][] x = {{"foo"}}`)
4. 모든 이항/삼항 연산자 양쪽:
   - 결합 타입 바운드의 `&`: `<T extends Foo & Bar>`
   - 다중 catch의 `|`: `catch (FooException | BarException e)`
   - 향상된 for의 `:`: `for (String s : list)`
   - 람다 `->`: `(String str) -> str.length()`
   - **예외**: `::` (메서드 참조), `.` (멤버 접근)은 공백 없음
5. `,` `:` `;` 또는 캐스트의 닫는 `)` 뒤
6. 내용과 `//` 사이 (다중 공백 허용)
7. `//`와 주석 텍스트 사이 (다중 공백 허용)
8. 타입과 변수 선언 사이: `List<String> list`
9. 배열 이니셜라이저 중괄호 안쪽 (선택): `{5, 6}`과 `{ 5, 6 }` 모두 유효

**수평 정렬**: 필수 아님. 정렬 유지 권장하지 않음.

### 6.7 그룹화 괄호

- 저자와 리뷰어가 코드 오해 가능성이 없다고 동의할 때만 선택적 괄호 생략
- 모든 독자가 Java 연산자 우선순위 표를 암기하고 있다고 가정 금지

### 6.8 특정 구문

#### Enum 클래스

- enum 상수 뒤 줄 바꿈은 선택적
- 메서드와 문서가 없는 enum은 배열 이니셜라이저로 포매팅 가능: `enum Suit { CLUBS, HEARTS, SPADES, DIAMONDS }`

#### 변수 선언

- **선언당 하나의 변수**: `int a, b;` 금지. 예외: `for` 루프 헤더
- 지역 변수는 **첫 사용 지점 가까이** 선언 (블록 시작이 아님)

#### 배열

- 배열 선언 C 스타일 금지: `String[] args` (O), `String args[]` (X)

#### Switch 문

- 블록 내용 switch 키워드에서 +2 들여쓰기
- 이전 스타일 switch: 각 문장 그룹은 `break`, `continue`, `return`, `throw`로 종료하거나 fall-through 주석 (예: `// fall through`) 필수
- 새 스타일(화살표) switch: fall-through 없음
- 모든 switch는 **완전해야** (모든 가능한 값 커버)
- Switch 표현식은 새 스타일(화살표) 구문 필수

#### 어노테이션

- 타입 사용 어노테이션: 주석 달린 타입 바로 앞: `final @Nullable String name;`
- 클래스/패키지/모듈 어노테이션: 각각 자체 줄
- 메서드/생성자 어노테이션: 클래스와 동일. 단, 파라미터 없는 단일 어노테이션은 시그니처 첫 줄과 공유 가능: `@Override public int hashCode() { ... }`
- 필드 어노테이션: 여러 어노테이션이 한 줄 공유 가능: `@Partial @Mock DataLoader loader;`

#### 주석

- 블록 주석: 주변 코드와 같은 수준 들여쓰기
- 장식 상자 금지 (별표/문자로 감싸지 않음)
- TODO 주석: `TODO:` + 리소스 링크/컨텍스트 + 하이픈 + 설명 텍스트

#### 수정자 순서 (JLS 권장)

```
public protected private abstract default static final sealed non-sealed
transient volatile synchronized native strictfp
```

#### 숫자 리터럴

- `long` 정수 리터럴: 대문자 `L` 접미사 (소문자 `l` 금지 - 숫자 `1`과 혼동)

#### 텍스트 블록

- 여는 `"""`는 항상 새 줄에
- 닫는 `"""`는 여는/닫는 구분자와 같은 들여쓰기로 새 줄에
- 텍스트 블록 내용은 100자 칼럼 제한 초과 가능

---

## 7. 네이밍

### 7.1 보편 규칙

- 식별자에 ASCII 문자, 숫자, 특정 경우 언더스코어만 사용
- 특수 접두/접미사 금지: `name_`, `mName`, `s_name`, `kName` 금지

### 7.2 식별자 유형별 규칙

| 식별자 | 케이스 스타일 | 추가 규칙 |
|---|---|---|
| 패키지 | `com.example.deepspace` | 전소문자, 언더스코어 없음, 연속 단어 연결 |
| 클래스 | `UpperCamelCase` | 보통 명사/명사구. 테스트 클래스는 `Test`로 끝남 |
| 메서드 | `lowerCamelCase` | 보통 동사/동사구. 테스트 메서드는 `_` 구분 허용 |
| 상수 | `UPPER_SNAKE_CASE` | `static final` + 깊이 불변 값 + 부작용 없음 필수 |
| 비상수 필드 | `lowerCamelCase` | 보통 명사/명사구 |
| 파라미터 | `lowerCamelCase` | 공개 메서드에서 단일 문자 이름 회피 |
| 지역 변수 | `lowerCamelCase` | `final`이고 불변이어도 상수 스타일 금지 |
| 타입 변수 | 단일 대문자(+숫자) 또는 클래스명+`T` | `E`, `T`, `T2`, `RequestT`, `FooBarT` |

### 7.3 상수 vs 비상수 (중요 구분)

**상수** (`UPPER_SNAKE_CASE`):
```java
static final int NUMBER = 5;
static final ImmutableList<String> NAMES = ImmutableList.of("Ed", "Ann");
static final Joiner COMMA_JOINER = Joiner.on(',');
static final SomeMutableType[] EMPTY_ARRAY = {};
```

**비상수** (`lowerCamelCase`):
```java
static String nonFinal = "non-final";              // final 아님
final String nonStatic = "non-static";              // static 아님
static final Set<String> mutableCollection = new HashSet<>();  // 가변
static final Logger logger = Logger.getLogger(...); // 부작용
static final String[] nonEmptyArray = {"these", "can", "change"};  // 가변 내용
```

### 7.4 CamelCase 변환 알고리즘

1. 순수 ASCII로 변환, 아포스트로피 제거
2. 공백과 나머지 구두점으로 단어 분리. 기존 camelCase도 분리 권장
3. 모두 소문자화, 각 단어 첫 글자 대문자화 (UpperCamelCase) 또는 첫 단어 제외 (lowerCamelCase)
4. 모든 단어 결합

| 산문 형태 | 올바름 | 잘못됨 |
|---|---|---|
| "XML HTTP request" | `XmlHttpRequest` | `XMLHTTPRequest` |
| "new customer ID" | `newCustomerId` | `newCustomerID` |
| "supports IPv6 on iOS?" | `supportsIpv6OnIos` | `supportsIPv6OnIOS` |
| "YouTube importer" | `YouTubeImporter` 또는 `YoutubeImporter` | -- |

---

## 8. 프로그래밍 관행

### 8.1 @Override

- 합법적으로 허용되는 모든 곳에서 항상 `@Override` 사용: 슈퍼클래스 메서드 오버라이딩, 인터페이스 메서드 구현, 슈퍼인터페이스 메서드 재지정, 레코드 컴포넌트 명시적 접근자
- 예외: 부모 메서드가 `@Deprecated`일 때 생략 가능

### 8.2 캐치된 예외

- 절대 조용히 무시하지 않음. 반응: 로깅, 재throw, 래핑(`AssertionError`), 또는 적절한 처리
- 정당하게 아무 조치도 안 할 때: 주석으로 정당화

```java
try {
  int i = Integer.parseInt(response);
  return handleNumericResponse(i);
} catch (NumberFormatException ok) {
  // it's not numeric; that's fine, just continue
}
return handleTextResponse(response);
```

### 8.3 정적 멤버

- 항상 **클래스 이름**으로 한정. 인스턴스 참조 통한 접근 금지
- 올바름: `Foo.aStaticMethod()`
- 잘못됨: `someFooInstance.aStaticMethod()`

### 8.4 Finalizer

- **절대** `Object.finalize()` 오버라이드 금지. JDK에서 제거 예정.

---

## 9. Javadoc

### 9.1 일반 포맷

**여러 줄 형태**:
```java
/**
 * Multiple lines of Javadoc text,
 * wrapped normally...
 */
```

**한 줄 형태** (전체 Javadoc이 한 줄에 맞고 블록 태그 없을 때):
```java
/** An especially short bit of Javadoc. */
```

### 9.2 문단

- 정렬된 선행 `*`만 포함하는 빈 줄로 문단 구분
- 첫 번째 이후 각 문단은 첫 단어 바로 앞에 `<p>` (`<p>` 뒤 공백 없음)
- 블록 수준 HTML 요소(`<ul>`, `<table>`)는 `<p>` 앞에 두지 않음

### 9.3 블록 태그

- 표준 순서: `@param`, `@return`, `@throws`, `@deprecated`
- 블록 태그에 빈 설명 금지
- 블록 태그 연속 줄: `@` 위치에서 4칸 이상 들여쓰기

### 9.4 요약 조각

- 모든 Javadoc 블록의 첫 번째 문장/조각
- 명사구 또는 동사구, **완전한 문장 아님**
- 완전한 문장처럼 대문자화하고 구두점 처리
- `"A {@code Foo} is a..."`로 시작 금지
- `"This method returns..."`로 시작 금지
- `@return` 전용 메서드: `/** Returns the customer ID. */` 선호

### 9.5 Javadoc 필수 대상

**필수**: 모든 가시적 클래스, 멤버, 레코드 컴포넌트 (`public` 또는 `protected`).

**예외**:
- 자명한 멤버 (예: `getFoo()` -- 추가 설명 없음). 남용 금지
- 슈퍼타입 메서드를 오버라이드하는 메서드

**비필수이나 권장**: 기타 클래스/멤버. 구현 주석 대신 Javadoc(`/** */`) 사용 권장.

---

## 10. 핵심 수치 요약

| 카테고리 | 규칙 | 값 |
|---|---|---|
| 인코딩 | 소스 파일 인코딩 | UTF-8 |
| 들여쓰기 | 블록 들여쓰기 | 2칸 공백 |
| 들여쓰기 | 연속 들여쓰기 | +4칸 이상 |
| 칼럼 제한 | 최대 줄 길이 | 100자 |
| 중괄호 | 스타일 | K&R (Egyptian) |
| 중괄호 | 단일 문장 본문 | 항상 필수 |
| Import | 와일드카드 import | 금지 |
| Import | 순서 | 정적 블록, 빈 줄, 비정적 블록; 내부 ASCII 정렬 |
| 클래스 | 파일당 | 정확히 하나 |
| 변수 | 선언당 | 정확히 하나 |
| 변수 | 선언 위치 | 첫 사용 지점 가까이 |
| 배열 | 선언 스타일 | `String[] args` (`String args[]` 아님) |
| Switch | 완전성 | 모든 switch에 필수 |
| Switch 표현식 | 스타일 | 화살표 구문 필수 |
| `@Override` | 사용 | 합법적 시 항상 |
| 예외 | 캐치된 예외 | 절대 조용히 무시 금지 |
| 정적 멤버 | 접근 | 클래스 이름으로만 |
| `finalize()` | 오버라이드 | 금지 |
| `long` 리터럴 | 접미사 | 대문자 `L` |
| 네이밍: 패키지 | 스타일 | `alllowercase` |
| 네이밍: 클래스 | 스타일 | `UpperCamelCase` |
| 네이밍: 메서드 | 스타일 | `lowerCamelCase` |
| 네이밍: 상수 | 스타일 | `UPPER_SNAKE_CASE` |
| 네이밍: 필드/파라미터/지역변수 | 스타일 | `lowerCamelCase` |
| 네이밍: 타입 변수 | 스타일 | `T`, `E`, `T2`, 또는 `RequestT` |
| Javadoc | 필수 대상 | 모든 가시적 멤버 |
| Javadoc | 요약 조각 | 명사/동사구, 문장 아님 |
| Javadoc | 블록 태그 순서 | `@param`, `@return`, `@throws`, `@deprecated` |

---

## 참고 자료

- [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
- [Google Styleguide GitHub Repository](https://github.com/google/styleguide)
- [google-java-format Tool](https://github.com/google/google-java-format)
- [Oracle Java Code Conventions](https://www.oracle.com/java/technologies/javase/codeconventions-contents.html)
- [Alibaba Java Coding Guidelines](https://github.com/alibaba/Alibaba-Java-Coding-Guidelines)
- [Spring Framework Code Style](https://github.com/spring-projects/spring-framework/wiki/Code-Style)
