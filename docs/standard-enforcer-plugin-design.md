# Standard Enforcer Plugin 설계 문서

> 코딩 컨벤션과 표준 용어 사전을 기반으로 일관된 코드 작성을 지원하는 Claude Code 플러그인

---

## 1. 개요

### 1.1 목적

`/Volumes/data/claude/standard` 에 정의된 **코딩 컨벤션**(Java, TypeScript, Python)과 **표준 용어 사전**(단어, 용어, 도메인)을 Claude Code가 코드를 작성할 때 자동으로 참조하고 준수하도록 하는 플러그인을 개발한다.

### 1.2 해결하려는 문제

| 문제 | 현상 | 플러그인 해결 방식 |
|---|---|---|
| 컨벤션 불일치 | 개발자마다 다른 네이밍, 포맷팅, 코드 스타일 | 언어별 컨벤션을 자동 참조하여 코드 생성 |
| 비표준 용어 사용 | DB 컬럼명, 변수명에 자의적 영문 약어 사용 | 표준 용어 사전(13,176건) 기반 네이밍 |
| 도메인 규칙 무시 | 데이터 타입, 길이, 포맷 불일치 | 123개 표준 도메인 규칙 자동 적용 |
| 금칙어 사용 | 금지된 단어나 비표준 동의어 사용 | 금칙어/이음동의어 검출 및 표준어 대체 제안 |

### 1.3 대상 사용자

- Claude Code를 사용하여 Java, TypeScript, Python 프로젝트를 개발하는 팀
- 공공기관 데이터 표준을 준수해야 하는 SI/SM 프로젝트 팀
- 사내 코딩 컨벤션 통일이 필요한 조직

---

## 2. 플러그인 아키텍처

### 2.1 디렉토리 구조

```
standard-enforcer/
├── .claude-plugin/
│   └── plugin.json                          # 플러그인 매니페스트
├── commands/
│   ├── check-convention.md                  # /standard-enforcer:check-convention
│   ├── lookup-term.md                       # /standard-enforcer:lookup-term
│   ├── check-naming.md                      # /standard-enforcer:check-naming
│   └── generate-entity.md                   # /standard-enforcer:generate-entity
├── skills/
│   ├── coding-convention/
│   │   ├── SKILL.md                         # 코딩 컨벤션 자동 적용 스킬
│   │   ├── java-coding-convention.md        # Java 컨벤션 참조 문서
│   │   ├── typescript-coding-convention.md  # TypeScript 컨벤션 참조 문서
│   │   └── python-coding-convention.md      # Python 컨벤션 참조 문서
│   └── data-standard/
│       ├── SKILL.md                         # 표준 용어 자동 적용 스킬
│       └── data-standard-terminology-guide.md  # 용어 사전 활용 가이드
├── agents/
│   ├── convention-checker.md                # 컨벤션 검사 에이전트
│   └── naming-validator.md                  # 네이밍 검증 에이전트
├── hooks/
│   └── hooks.json                           # 훅 설정
├── scripts/
│   ├── check-forbidden-words.sh             # 금칙어 검사 스크립트
│   └── validate-naming.sh                   # 네이밍 규칙 검사 스크립트
├── data/
│   ├── standard_words.json                  # 표준 단어 사전 (3,284건)
│   ├── standard_terms.json                  # 표준 용어 사전 (13,176건)
│   └── standard_domains.json                # 표준 도메인 (123건)
├── README.md
├── LICENSE
└── CHANGELOG.md
```

### 2.2 매니페스트 (plugin.json)

```json
{
  "name": "standard-enforcer",
  "version": "1.0.0",
  "description": "코딩 컨벤션(Java/TypeScript/Python)과 공공 데이터 표준 용어 사전을 기반으로 일관된 코드 작성을 지원하는 플러그인",
  "author": {
    "name": "Team",
    "email": "dev@example.com"
  },
  "license": "MIT",
  "keywords": [
    "coding-convention",
    "data-standard",
    "naming",
    "java",
    "typescript",
    "python",
    "korean-standard"
  ]
}
```

---

## 3. 컴포넌트 상세 설계

### 3.1 스킬 (Skills) - Claude 자동 호출

#### 3.1.1 coding-convention 스킬

**목적**: Claude가 코드를 작성하거나 수정할 때 해당 언어의 코딩 컨벤션을 **자동으로 참조**한다.

**파일**: `skills/coding-convention/SKILL.md`

```yaml
---
name: coding-convention
description: >
  코드를 작성하거나 수정할 때 프로젝트의 코딩 컨벤션을 자동 적용합니다.
  Java, TypeScript, Python 파일을 편집하거나 새로 생성할 때,
  코드 리뷰나 리팩토링을 수행할 때 사용합니다.
---
```

**프롬프트 내용**:

```markdown
# 코딩 컨벤션 적용 스킬

코드를 작성하거나 수정할 때 반드시 아래 컨벤션을 따르라.

## 언어 판별
- 작업 대상 파일의 확장자로 언어를 판별한다
  - `.java` → Java 컨벤션 적용
  - `.ts`, `.tsx` → TypeScript 컨벤션 적용
  - `.py` → Python 컨벤션 적용

## Java 컨벤션 (Google Java Style Guide 기반)
참조: java-coding-convention.md

핵심 규칙:
- 인덴트: 2스페이스, 연속줄 +4스페이스
- 줄 길이: 100자
- 중괄호: K&R 스타일, 단일 문장도 항상 사용
- 네이밍: 패키지(소문자), 클래스(UpperCamelCase), 메서드(lowerCamelCase), 상수(UPPER_SNAKE_CASE)
- import: 와일드카드 금지, static/non-static 분리, ASCII 정렬
- Javadoc: 모든 public/protected 멤버에 필수

## TypeScript 컨벤션 (Google TypeScript Style Guide 기반)
참조: typescript-coding-convention.md

핵심 규칙:
- 세미콜론 필수, Prettier 포맷팅
- 네이밍: 클래스/인터페이스(UpperCamelCase), 변수/함수(lowerCamelCase), 상수(CONSTANT_CASE), 파일(snake_case)
- export default 금지, named export만 사용
- any 금지 → unknown 사용
- var 금지, const 우선, let은 재할당 시에만
- .forEach() 금지 → for...of 사용
- 항상 === / !== (== null 예외)

## Python 컨벤션 (PEP 8 기반)
참조: python-coding-convention.md

핵심 규칙:
- 인덴트: 4스페이스, 탭 금지
- 줄 길이: 79자 (주석/독스트링 72자)
- 네이밍: 패키지(소문자), 모듈(lowercase_underscore), 클래스(CapWords), 함수/메서드(lowercase_underscore), 상수(UPPER_CASE)
- import 순서: 표준 라이브러리 → 서드파티 → 로컬
- 와일드카드 import 금지
- is None / is not None 사용 (== None 금지)
- isinstance() 사용 (type() 금지)
```

**참조 문서**: `skills/coding-convention/` 디렉토리에 3개 컨벤션 원본을 그대로 포함하여, Claude가 세부 규칙까지 참조할 수 있게 한다.

#### 3.1.2 data-standard 스킬

**목적**: DB 관련 코드(엔티티 클래스, SQL, 마이그레이션 등)를 작성할 때 표준 용어 사전을 **자동으로 참조**한다.

**파일**: `skills/data-standard/SKILL.md`

```yaml
---
name: data-standard
description: >
  데이터베이스 테이블, 엔티티 클래스, SQL, 마이그레이션 파일을 작성할 때
  공공 데이터 표준 용어 사전을 자동 적용합니다. DB 컬럼명, 테이블명,
  변수명에 표준 영문 약어를 사용하고 도메인 규칙을 준수합니다.
---
```

**프롬프트 내용**:

```markdown
# 데이터 표준 용어 적용 스킬

DB 관련 코드를 작성할 때 반드시 아래 표준을 따르라.
참조: data-standard-terminology-guide.md

## 적용 대상 파일
- JPA/Hibernate 엔티티 클래스 (.java)
- TypeORM/Prisma 엔티티/스키마 (.ts)
- SQLAlchemy/Django 모델 (.py)
- SQL 파일 (.sql)
- 마이그레이션 파일
- DTO/VO 클래스

## 핵심 규칙

### 1. 컬럼명은 표준 용어 영문 약어를 사용한다
- 표준 용어 사전(data/ 디렉토리)에서 해당 한글 용어를 검색
- 표준 영문 약어가 있으면 반드시 그것을 사용
- 예: 고객명 → CSTMR_NM, 가입일자 → JOIN_YMD, 사업자등록번호 → BRNO

### 2. 접미사 패턴을 준수한다
| 접미사 | 의미 | 예시 |
|---|---|---|
| _YMD | 연월일 | REG_YMD (등록일자) |
| _DT | 일시 | REG_DT (등록일시) |
| _AMT | 금액 | STLM_AMT (결제금액) |
| _NM | 명 | CSTMR_NM (고객명) |
| _CD | 코드 | STTS_CD (상태코드) |
| _NO | 번호 | ACNT_NO (계좌번호) |
| _CN | 내용 | RMK_CN (비고내용) |
| _CNT | 건수 | INQR_CNT (조회건수) |
| _RT | 율 | TAX_RT (세율) |
| _YN | 여부 | USE_YN (사용여부) |
| _SN | 순번 | SRT_SN (정렬순번) |
| _ADDR | 주소 | BASS_ADDR (기본주소) |

### 3. 테이블명 규칙
- TB_[업무영역코드]_[엔티티명코드]: 일반 테이블
- TC_: 코드 테이블
- TH_: 이력 테이블
- TL_: 로그 테이블
- TR_: 관계 테이블

### 4. 도메인 규칙에 따른 데이터 타입 매핑
- 연월일(C8) → Java: LocalDate, TS: string, Python: date
- 일시(D) → Java: LocalDateTime, TS: Date, Python: datetime
- 금액(N15) → Java: BigDecimal, TS: number, Python: Decimal
- 명(V100) → Java: String, TS: string, Python: str
- 여부(C1) → Java: String("Y"/"N"), TS: "Y"|"N", Python: str

### 5. 금칙어 사용 금지
- 표준 단어 사전의 '금칙어목록'에 있는 단어 사용 금지
- 발견 시 이음동의어목록의 표준어로 대체

## 용어 검색 방법
DB 관련 네이밍이 필요하면:
1. data/standard_terms.json에서 한글 용어명으로 검색
2. 해당 용어의 '공통표준용어영문약어명' 값을 컬럼명으로 사용
3. '공통표준도메인명'으로 데이터 타입과 길이 결정
4. 표준 용어에 없는 경우, data/standard_words.json에서 개별 단어를 조합
```

---

### 3.2 커맨드 (Commands) - 사용자 수동 호출

#### 3.2.1 /standard-enforcer:check-convention

**목적**: 지정한 파일 또는 변경사항에 대해 코딩 컨벤션 준수 여부를 검사한다.

**파일**: `commands/check-convention.md`

```markdown
---
description: 코드의 코딩 컨벤션 준수 여부를 검사하고 위반 사항을 보고합니다
---

# 코딩 컨벤션 검사

$ARGUMENTS 에 대해 코딩 컨벤션 준수 여부를 검사하라.

## 검사 절차

1. 대상 파일을 읽고 언어를 판별한다 (.java/.ts/.tsx/.py)
2. 해당 언어의 코딩 컨벤션 참조 문서를 확인한다
3. 아래 항목을 순서대로 검사한다

## 검사 항목

### 공통
- [ ] 파일 인코딩 (UTF-8)
- [ ] 줄 길이 제한 준수
- [ ] 들여쓰기 규칙 (스페이스/탭)
- [ ] import 순서 및 와일드카드 사용
- [ ] 네이밍 컨벤션 (클래스, 메서드, 변수, 상수)

### Java 전용
- [ ] K&R 중괄호 스타일
- [ ] @Override 어노테이션 사용
- [ ] Javadoc 작성 여부 (public/protected)
- [ ] static 멤버 접근 방식

### TypeScript 전용
- [ ] export default 사용 여부 (금지)
- [ ] any 타입 사용 여부 (금지)
- [ ] var 사용 여부 (금지)
- [ ] .forEach() 사용 여부 (금지)
- [ ] === / !== 사용 여부

### Python 전용
- [ ] PEP 8 네이밍 (snake_case 함수, CapWords 클래스)
- [ ] is None 사용 여부
- [ ] isinstance() 사용 여부
- [ ] with 문 사용 여부 (리소스 관리)

## 출력 형식

위반 사항을 아래 형식으로 보고하라:

| 심각도 | 파일:라인 | 규칙 | 현재 코드 | 수정 제안 |
|---|---|---|---|---|
| Error | src/Main.java:15 | 네이밍 | `myVariable` | `my_variable` (Python의 경우) |
| Warning | ... | ... | ... | ... |

마지막에 총 위반 건수와 자동 수정 가능 건수를 요약하라.
```

#### 3.2.2 /standard-enforcer:lookup-term

**목적**: 한글 용어를 입력하면 표준 영문 약어, 도메인, 데이터 타입을 조회한다.

**파일**: `commands/lookup-term.md`

```markdown
---
description: 표준 용어 사전에서 한글 용어의 영문 약어, 도메인, 데이터 타입을 조회합니다
---

# 표준 용어 조회

"$ARGUMENTS" 에 해당하는 표준 용어를 조회하라.

## 조회 절차

1. data/standard_terms.json에서 '공통표준용어명'을 검색한다
2. 정확히 일치하는 용어가 있으면 상세 정보를 출력한다
3. 정확히 일치하지 않으면 부분 일치 결과를 최대 10건 보여준다
4. 용어가 없으면 data/standard_words.json에서 개별 단어를 조합하여 제안한다

## 출력 형식

### 정확히 일치하는 경우

| 항목 | 값 |
|---|---|
| 표준 용어명 | (한글) |
| 영문 약어명 | (물리 컬럼명) |
| 용어 설명 | (설명) |
| 도메인 | (도메인명) |
| 데이터 타입 | (타입 + 길이) |
| 저장 형식 | (포맷) |
| 표현 형식 | (화면 포맷) |
| 허용값 | (있는 경우) |
| 이음동의어 | (있는 경우) |

### Java / TypeScript / Python 매핑

각 언어에서 사용할 타입도 함께 표시하라:
- Java: `LocalDate`, `BigDecimal`, `String` 등
- TypeScript: `string`, `number`, `Date` 등
- Python: `date`, `Decimal`, `str` 등
```

#### 3.2.3 /standard-enforcer:check-naming

**목적**: 기존 코드의 DB 관련 네이밍이 표준 용어 사전을 준수하는지 검사한다.

**파일**: `commands/check-naming.md`

```markdown
---
description: DB 관련 코드의 네이밍이 표준 용어 사전을 준수하는지 검사합니다
---

# 표준 네이밍 검사

$ARGUMENTS 에 대해 DB 관련 네이밍의 표준 준수 여부를 검사하라.

## 검사 대상
- 엔티티 클래스의 @Column, @Table 어노테이션
- SQL DDL의 CREATE TABLE, ALTER TABLE 문
- ORM 스키마 정의 (Prisma, TypeORM, SQLAlchemy, Django)
- DTO/VO 클래스의 필드명

## 검사 항목

1. **표준 용어 일치**: 컬럼명이 standard_terms.json의 영문약어와 일치하는가
2. **접미사 패턴**: _YMD, _AMT, _NM 등 표준 접미사를 올바르게 사용하는가
3. **도메인 규칙**: 데이터 타입과 길이가 도메인 정의와 일치하는가
4. **금칙어 사용**: standard_words.json의 금칙어목록에 해당하는 단어가 있는가
5. **테이블명 규칙**: TB_/TC_/TH_/TL_/TR_ 접두사를 올바르게 사용하는가

## 출력 형식

| 유형 | 위치 | 현재 네이밍 | 표준 네이밍 | 비고 |
|---|---|---|---|---|
| 비표준 컬럼명 | User.java:25 | `cust_name` | `CSTMR_NM` | 표준 용어: 고객명 |
| 금칙어 사용 | Order.java:30 | `reg_date` | `REG_YMD` | '날짜' → '일자' (표준어) |
| 도메인 불일치 | User.java:28 | `VARCHAR(50)` | `VARCHAR(100)` | 명V100 도메인 |
```

#### 3.2.4 /standard-enforcer:generate-entity

**목적**: 한글 테이블/컬럼 정의를 입력하면 표준을 준수하는 엔티티 코드를 생성한다.

**파일**: `commands/generate-entity.md`

```markdown
---
description: 한글 테이블/컬럼 정의로부터 표준을 준수하는 엔티티 코드를 생성합니다
---

# 표준 엔티티 생성

$ARGUMENTS 의 한글 테이블/컬럼 정의를 바탕으로 엔티티 코드를 생성하라.

## 입력 형식 예시

"고객 테이블: 고객명, 고객번호, 생년월일, 이메일주소, 가입일자, 사용여부"

## 생성 절차

1. 각 한글 컬럼명을 data/standard_terms.json에서 검색한다
2. 표준 용어가 있으면 영문 약어, 도메인, 데이터 타입을 가져온다
3. 표준 용어가 없으면 data/standard_words.json에서 단어를 조합한다
4. 프로젝트 언어에 맞는 엔티티 클래스를 생성한다

## 출력

### 1. 용어 매핑 테이블

| 한글 컬럼명 | 영문 약어 | 도메인 | 데이터 타입 | 출처 |
|---|---|---|---|---|
| 고객명 | CSTMR_NM | 명V100 | VARCHAR(100) | 표준용어 |

### 2. 엔티티 코드
해당 언어의 코딩 컨벤션을 준수하여 생성한다:
- Java: JPA @Entity, @Column 어노테이션 포함
- TypeScript: TypeORM 또는 Prisma 스키마
- Python: SQLAlchemy 또는 Django 모델

### 3. DDL (CREATE TABLE)
표준 테이블명 규칙(TB_ 접두사)을 적용한 SQL DDL도 함께 생성한다.
```

---

### 3.3 서브에이전트 (Agents)

#### 3.3.1 convention-checker 에이전트

**목적**: 코드 변경사항 전체에 대해 심층적인 컨벤션 검사를 수행하는 전문 에이전트.

**파일**: `agents/convention-checker.md`

```yaml
---
name: convention-checker
description: >
  코드 변경사항에 대해 Java/TypeScript/Python 코딩 컨벤션을 심층 검사합니다.
  코드 리뷰, PR 체크, 코드 품질 분석 시 사용합니다.
---
```

**프롬프트 내용**:

```markdown
당신은 코딩 컨벤션 검사 전문가 에이전트입니다.

## 역할
코드 변경사항에서 코딩 컨벤션 위반을 식별하고 수정안을 제시합니다.

## 참조 기준
- Java: Google Java Style Guide (2스페이스 인덴트, 100자 줄 제한, K&R 중괄호)
- TypeScript: Google TypeScript Style Guide (export default 금지, any 금지, === 필수)
- Python: PEP 8 (4스페이스 인덴트, 79자 줄 제한, snake_case 함수)

## 검사 카테고리

### 1. 포맷팅 (Formatting)
- 인덴트, 줄 길이, 중괄호 스타일, 공백 규칙

### 2. 네이밍 (Naming)
- 클래스, 메서드, 변수, 상수, 파일명의 케이스 규칙

### 3. 구조 (Structure)
- import 순서, 선언 순서, 파일 구조

### 4. 언어별 금지 패턴 (Prohibited Patterns)
- Java: 와일드카드 import, finalize() 오버라이드
- TypeScript: var, any, export default, .forEach(), const enum
- Python: bare except, == None, type() 비교

## 출력 형식
각 위반 사항에 대해:
- **심각도**: Error / Warning / Info
- **카테고리**: 포맷팅 / 네이밍 / 구조 / 금지패턴
- **위치**: 파일명:라인번호
- **규칙**: 위반된 규칙 명칭
- **현재**: 현재 코드
- **수정안**: 컨벤션 준수 코드
- **신뢰도**: 0-100%
```

#### 3.3.2 naming-validator 에이전트

**목적**: DB 관련 네이밍에 대해 표준 용어 사전 기반 심층 검증을 수행하는 전문 에이전트.

**파일**: `agents/naming-validator.md`

```yaml
---
name: naming-validator
description: >
  DB 엔티티, SQL, DTO의 네이밍이 공공 데이터 표준 용어 사전을 준수하는지 검증합니다.
  데이터 모델링, 엔티티 작성, SQL 작성, DTO 설계 시 사용합니다.
---
```

**프롬프트 내용**:

```markdown
당신은 데이터 표준 네이밍 검증 전문가 에이전트입니다.

## 역할
DB 관련 코드에서 비표준 네이밍을 식별하고 표준 용어를 제안합니다.

## 참조 데이터
- data/standard_terms.json: 표준 용어 (13,176건)
- data/standard_words.json: 표준 단어 (3,284건)
- data/standard_domains.json: 표준 도메인 (123건)

## 검증 항목

### 1. 컬럼명 표준 준수
- 물리 컬럼명이 표준 용어의 영문 약어와 일치하는지 확인
- 불일치 시 가장 유사한 표준 용어 제안

### 2. 접미사 패턴 일관성
- _YMD, _DT, _AMT, _NM, _CD, _NO, _CN, _CNT, _RT, _YN, _SN, _ADDR
- 의미와 접미사가 일치하는지 확인 (날짜인데 _NM이면 오류)

### 3. 도메인 규칙 준수
- 데이터 타입과 길이가 도메인 정의와 일치하는지 확인
- VARCHAR(50)인데 도메인이 명V100이면 길이 불일치 보고

### 4. 금칙어 검출
- standard_words.json의 '금칙어목록' 필드를 기반으로 검출
- 금칙어 발견 시 '이음동의어목록'에서 표준어 제안

### 5. 테이블명 규칙
- TB_, TC_, TH_, TL_, TR_ 접두사 확인
- 접두사 누락 또는 오사용 보고

## 출력 형식
각 발견사항에 대해:
- **유형**: 비표준용어 / 접미사오류 / 도메인불일치 / 금칙어 / 테이블명오류
- **위치**: 파일명:라인번호
- **현재값**: 현재 네이밍
- **표준값**: 표준 네이밍
- **근거**: 참조한 표준 용어/단어/도메인
- **신뢰도**: 0-100%
```

---

### 3.4 훅 (Hooks)

#### 3.4.1 hooks.json

**목적**: 코드 작성/수정 시 자동으로 컨벤션 및 네이밍 검사를 실행한다.

**파일**: `hooks/hooks.json`

```json
{
  "description": "코딩 컨벤션 및 표준 용어 자동 검사 훅",
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/check-forbidden-words.sh",
            "timeout": 30,
            "statusMessage": "금칙어 검사 중..."
          },
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/validate-naming.sh",
            "timeout": 30,
            "statusMessage": "네이밍 규칙 검사 중..."
          }
        ]
      }
    ]
  }
}
```

#### 3.4.2 금칙어 검사 스크립트

**파일**: `scripts/check-forbidden-words.sh`

**동작**:
1. stdin으로 PostToolUse 이벤트 JSON을 수신한다
2. `tool_input.file_path`에서 대상 파일 경로를 추출한다
3. DB 관련 파일인지 판별한다 (엔티티, 모델, SQL, 마이그레이션 등)
4. 파일 내 식별자를 추출한다
5. `data/standard_words.json`의 `금칙어목록`과 대조한다
6. 금칙어 발견 시 stdout에 경고 JSON을 출력하고 exit 0으로 반환한다 (비블로킹)

```bash
#!/bin/bash
# scripts/check-forbidden-words.sh
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // .tool_input.filePath // empty')

# 파일 경로가 없으면 종료
if [ -z "$FILE_PATH" ]; then
  exit 0
fi

# DB 관련 파일만 검사 (엔티티, 모델, SQL, 마이그레이션 등)
case "$FILE_PATH" in
  *entity*|*Entity*|*model*|*Model*|*.sql|*migration*|*Migration*|*dto*|*Dto*|*DTO*|*vo*|*Vo*|*VO*|*schema*|*Schema*)
    ;;
  *)
    exit 0
    ;;
esac

# 금칙어 검사 로직
PLUGIN_ROOT=$(dirname "$(dirname "$0")")
WORDS_FILE="$PLUGIN_ROOT/data/standard_words.json"

if [ ! -f "$WORDS_FILE" ]; then
  exit 0
fi

# 금칙어가 있는 단어 목록 추출 및 파일 내용과 대조
WARNINGS=""
while IFS= read -r line; do
  WORD=$(echo "$line" | jq -r '.[0]')
  FORBIDDEN=$(echo "$line" | jq -r '.[1]')
  STANDARD=$(echo "$line" | jq -r '.[2]')
  if grep -qi "$FORBIDDEN" "$FILE_PATH" 2>/dev/null; then
    WARNINGS="${WARNINGS}[금칙어] '${FORBIDDEN}' 발견 → 표준어 '${WORD}' 사용 권장\n"
  fi
done < <(jq -r '.[] | select(.금칙어목록 != null and .금칙어목록 != "") | [.공통표준단어명, .금칙어목록, .이음동의어목록] | @json' "$WORDS_FILE" 2>/dev/null | head -100)

if [ -n "$WARNINGS" ]; then
  echo -e "$WARNINGS"
fi

exit 0
```

#### 3.4.3 네이밍 규칙 검사 스크립트

**파일**: `scripts/validate-naming.sh`

**동작**:
1. DB 관련 파일에서 컬럼/테이블명을 추출한다
2. 접미사 패턴 일관성을 검사한다 (날짜에 _YMD, 금액에 _AMT 등)
3. 테이블명 접두사(TB_, TC_ 등)를 검사한다
4. 위반 사항을 stdout에 경고로 출력한다

```bash
#!/bin/bash
# scripts/validate-naming.sh
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // .tool_input.filePath // empty')

if [ -z "$FILE_PATH" ]; then
  exit 0
fi

# DB 관련 파일만 검사
case "$FILE_PATH" in
  *entity*|*Entity*|*model*|*Model*|*.sql|*migration*|*Migration*|*schema*|*Schema*)
    ;;
  *)
    exit 0
    ;;
esac

WARNINGS=""

# 테이블명 접두사 검사 (SQL 파일)
if [[ "$FILE_PATH" == *.sql ]]; then
  # CREATE TABLE 문에서 TB_, TC_, TH_, TL_, TR_ 접두사가 없는 경우 경고
  while IFS= read -r table_name; do
    if ! echo "$table_name" | grep -qE '^(TB_|TC_|TH_|TL_|TR_)'; then
      WARNINGS="${WARNINGS}[테이블명] '${table_name}': 표준 접두사(TB_/TC_/TH_/TL_/TR_) 누락\n"
    fi
  done < <(grep -ioP 'CREATE\s+TABLE\s+(?:IF\s+NOT\s+EXISTS\s+)?\K[A-Za-z_][A-Za-z0-9_]*' "$FILE_PATH" 2>/dev/null)
fi

if [ -n "$WARNINGS" ]; then
  echo -e "$WARNINGS"
fi

exit 0
```

---

## 4. 데이터 번들링

### 4.1 포함 데이터

`data/` 디렉토리에 아래 파일을 플러그인과 함께 번들한다:

| 파일 | 레코드 수 | 용도 |
|---|---|---|
| `standard_words.json` | 3,284 | 표준 단어 (영문 약어, 금칙어, 이음동의어) |
| `standard_terms.json` | 13,176 | 표준 용어 (컬럼명, 도메인, 데이터 타입) |
| `standard_domains.json` | 123 | 도메인 정의 (타입코드, 길이, 소수점) |

### 4.2 데이터 활용 흐름

```
사용자가 "고객명" 컬럼을 포함하는 엔티티 작성 요청
        ↓
[data-standard 스킬 자동 활성화]
        ↓
standard_terms.json 검색: "고객명"
        ↓
결과: 영문약어=CSTMR_NM, 도메인=명V100
        ↓
standard_domains.json 참조: 명V100 → VARCHAR(100)
        ↓
[coding-convention 스킬 자동 활성화]
        ↓
언어별 컨벤션 적용하여 코드 생성
        ↓
Java: @Column(name = "CSTMR_NM", length = 100)
      private String customerName;
        ↓
[PostToolUse 훅 실행]
        ↓
금칙어 검사 + 네이밍 규칙 검사
```

### 4.3 데이터 업데이트

표준 용어 사전은 행정안전부에서 연 1~2회 개정한다. 플러그인 업데이트 시 `data/` 디렉토리의 JSON 파일을 교체하면 된다.

---

## 5. 컴포넌트 상호작용

### 5.1 자동 흐름 (Skills + Hooks)

```
[코드 작성 작업 시작]
        │
        ├─→ coding-convention 스킬 (자동)
        │   └─ 해당 언어의 컨벤션을 참조하여 코드 생성
        │
        ├─→ data-standard 스킬 (자동, DB 관련 시)
        │   └─ 표준 용어 사전 참조하여 네이밍 결정
        │
        ▼
[Write/Edit 도구 실행]
        │
        ▼
[PostToolUse 훅 (자동)]
        ├─→ check-forbidden-words.sh: 금칙어 검사
        └─→ validate-naming.sh: 네이밍 규칙 검사
```

### 5.2 수동 흐름 (Commands + Agents)

```
[사용자가 커맨드 호출]
        │
        ├─→ /standard-enforcer:check-convention
        │   └─→ convention-checker 에이전트 (필요 시 자동 호출)
        │
        ├─→ /standard-enforcer:check-naming
        │   └─→ naming-validator 에이전트 (필요 시 자동 호출)
        │
        ├─→ /standard-enforcer:lookup-term
        │   └─ 표준 용어 즉시 조회
        │
        └─→ /standard-enforcer:generate-entity
            └─ 표준 준수 엔티티 코드 생성
```

---

## 6. 핵심 설계 결정

### 6.1 Skills vs Commands 분리 기준

| 컴포넌트 | 호출 방식 | 이유 |
|---|---|---|
| coding-convention (스킬) | Claude 자동 | 코드 작성 시 매번 자동으로 참조해야 하므로 |
| data-standard (스킬) | Claude 자동 | DB 코드 작성 시 자동으로 표준을 적용해야 하므로 |
| check-convention (커맨드) | 사용자 수동 | 명시적 검사 요청이 필요한 작업이므로 |
| lookup-term (커맨드) | 사용자 수동 | 특정 용어 조회는 의도적 행위이므로 |
| check-naming (커맨드) | 사용자 수동 | 기존 코드 전체 검사는 명시적 요청이 필요하므로 |
| generate-entity (커맨드) | 사용자 수동 | 코드 생성은 사용자 의도에 따라야 하므로 |

### 6.2 훅의 비블로킹 설계

PostToolUse 훅은 **비블로킹**으로 동작한다 (exit 0). 이유:

- 금칙어 검사나 네이밍 경고는 **정보 제공** 목적이다
- Claude의 작업 흐름을 중단시키면 생산성이 저하된다
- 심각한 위반은 스킬 레벨에서 사전에 방지된다
- 경고 메시지는 Claude의 컨텍스트에 추가되어 후속 작업에 반영된다

### 6.3 참조 문서 포함 방식

코딩 컨벤션 원본 문서(3개)를 `skills/coding-convention/` 디렉토리에 그대로 포함한다. 이유:

- Claude가 세부 규칙까지 정확히 참조할 수 있다
- SKILL.md에는 핵심 요약만 작성하여 토큰 효율성을 확보한다
- 필요시 Claude가 참조 문서를 읽어 세부 규칙을 확인한다

### 6.4 데이터 파일 번들링

표준 용어 사전 JSON 파일을 플러그인 `data/` 디렉토리에 직접 포함한다. 이유:

- 외부 API 의존성 없이 오프라인에서도 동작한다
- 플러그인 캐싱 시 데이터도 함께 복사된다
- JSON 파일을 Claude가 직접 읽고 검색할 수 있다
- 데이터 크기: 약 15MB (3개 파일 합산) — 합리적 수준

---

## 7. 테스트 계획

### 7.1 단위 테스트

| 테스트 항목 | 검증 방법 |
|---|---|
| 스킬 자동 활성화 | Java/TS/Python 파일 편집 시 컨벤션 참조 확인 |
| 용어 검색 정확도 | "고객명" → CSTMR_NM 매핑 확인 |
| 금칙어 검출 | 금칙어 포함 파일에서 경고 발생 확인 |
| 테이블명 접두사 검사 | TB_ 없는 CREATE TABLE에서 경고 확인 |
| 엔티티 생성 | 한글 정의 → 표준 준수 코드 생성 확인 |

### 7.2 통합 테스트

```bash
# 1. 플러그인 로컬 테스트
claude --plugin-dir ./standard-enforcer

# 2. 스킬 자동 호출 테스트
# → Java 엔티티 클래스 작성을 요청하여 컨벤션+표준용어 자동 적용 확인

# 3. 커맨드 테스트
/standard-enforcer:lookup-term 가입일자
/standard-enforcer:check-convention src/main/java/Customer.java
/standard-enforcer:check-naming src/main/java/entity/
/standard-enforcer:generate-entity "고객: 고객명, 가입일자, 이메일주소"

# 4. 훅 테스트
# → 엔티티 파일 저장 후 금칙어/네이밍 경고 확인

# 5. 디버깅
claude --debug
```

### 7.3 엣지 케이스

| 케이스 | 예상 동작 |
|---|---|
| 표준 용어에 없는 새 용어 | 단어 조합으로 제안, 불확실 시 명시 |
| 복수 언어 혼합 프로젝트 | 파일 확장자로 언어 판별, 각각 적용 |
| 대용량 파일 (1000줄+) | 스크립트 타임아웃(30초) 내 처리 |
| DB 관련 아닌 파일에 훅 실행 | 파일명 패턴 매칭으로 조기 종료 |
| 표준 단어 조합이 여러 개 가능 | 가장 일치도 높은 조합 제안 |

---

## 8. 배포 계획

### 8.1 로컬 배포 (1단계)

```bash
# 개발 완료 후 로컬에서 직접 사용
claude --plugin-dir /path/to/standard-enforcer
```

### 8.2 프로젝트 설정 배포 (2단계)

프로젝트의 `.claude/settings.json`에 등록하여 팀원 자동 설치:

```json
{
  "extraKnownMarketplaces": {
    "company-tools": {
      "source": {
        "source": "github",
        "repo": "team/claude-plugins"
      }
    }
  },
  "enabledPlugins": {
    "standard-enforcer@company-tools": true
  }
}
```

### 8.3 마켓플레이스 배포 (3단계)

GitHub 저장소로 공개하여 커뮤니티에서 사용 가능하게 한다.

---

## 9. 향후 확장 계획

| 단계 | 내용 | 우선순위 |
|---|---|---|
| v1.1 | PreToolUse 훅 추가 — 코드 작성 전 컨벤션 위반 사전 차단 | 높음 |
| v1.2 | 커스텀 용어 사전 지원 — 사내 전용 용어 추가 기능 | 중간 |
| v1.3 | MCP 서버 — 표준 용어 검색 API 제공 (대화형 조회) | 중간 |
| v2.0 | 다국어 컨벤션 확장 — Go, Rust, Kotlin 등 추가 | 낮음 |
| v2.1 | 자동 수정 기능 — 비표준 네이밍 자동 변환 (PreToolUse) | 낮음 |

---

## 부록: 컴포넌트 요약

| 컴포넌트 | 파일 경로 | 호출 방식 | 역할 |
|---|---|---|---|
| **coding-convention** (스킬) | skills/coding-convention/SKILL.md | 자동 | 코드 작성 시 언어별 컨벤션 참조 |
| **data-standard** (스킬) | skills/data-standard/SKILL.md | 자동 | DB 코드 작성 시 표준 용어 참조 |
| **check-convention** (커맨드) | commands/check-convention.md | /standard-enforcer:check-convention | 코딩 컨벤션 검사 |
| **lookup-term** (커맨드) | commands/lookup-term.md | /standard-enforcer:lookup-term | 표준 용어 조회 |
| **check-naming** (커맨드) | commands/check-naming.md | /standard-enforcer:check-naming | DB 네이밍 검사 |
| **generate-entity** (커맨드) | commands/generate-entity.md | /standard-enforcer:generate-entity | 표준 엔티티 생성 |
| **convention-checker** (에이전트) | agents/convention-checker.md | 자동/수동 | 심층 컨벤션 검사 |
| **naming-validator** (에이전트) | agents/naming-validator.md | 자동/수동 | 심층 네이밍 검증 |
| **PostToolUse 훅** | hooks/hooks.json | 자동 | Write/Edit 후 금칙어/네이밍 검사 |
| **금칙어 검사** (스크립트) | scripts/check-forbidden-words.sh | 훅에서 호출 | 금칙어 검출 및 표준어 제안 |
| **네이밍 검사** (스크립트) | scripts/validate-naming.sh | 훅에서 호출 | 테이블명/컬럼명 규칙 검사 |
