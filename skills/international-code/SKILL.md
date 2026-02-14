---
name: international-code
description: >
  전화번호 입력, 국가 선택, 지역 선택 등 국제 표준 코드를 다루는 컴포넌트를
  구현할 때, ISO 3166-1/3166-2 및 ITU-T E.164 표준 데이터를 기반으로
  코드를 작성하도록 자동 지원합니다.
---

# 국제 표준 코드 적용 스킬

전화번호, 국가코드, 지역코드 관련 컴포넌트를 구현할 때 반드시 아래 표준을 따르라.
상세 가이드는 이 디렉토리의 international-code-reference.md를 참조하라.

## 적용 대상

아래 조건에 해당하는 파일을 작성/수정할 때 자동 적용한다:

- 파일명에 `phone`, `tel`, `country`, `region`, `address`, `locale`, `i18n`, `intl` 포함
- 코드 내에 전화번호 입력, 국가 선택, 지역/주소 선택 관련 로직이 포함된 경우
- 국가코드(alpha-2/alpha-3), 지역코드(ISO 3166-2), 국제 전화번호 관련 데이터를 다루는 경우

## 참조 데이터 파일

| 파일 | 표준 | 내용 |
|---|---|---|
| `data/iso_3166_1_countries.json` | ISO 3166-1 | 249개국 — alpha-2, alpha-3, numeric, 한글명, 영문명, 독립국 여부 |
| `data/iso_3166_2_regions.json` | ISO 3166-2 | 21개국 653개 행정구역 — 지역코드, 영문명, 현지어명, 구역 유형 |
| `data/country_calling_codes.json` | ITU-T E.164 | 245개 항목 — 국제 전화 국번, ITU 존, 공유 코드 정보 |

## 핵심 규칙

### 1. 국가 식별에는 ISO 3166-1 alpha-2 코드를 사용한다

- 국가를 식별하는 모든 필드에 2자리 대문자 alpha-2 코드를 사용한다 (예: `KR`, `US`, `JP`)
- 풀네임 문자열이 아닌 코드로 저장한다 — 표시명은 `iso_3166_1_countries.json`에서 조회
- 3자리 코드가 필요한 경우 alpha-3를 사용한다 (예: `KOR`, `USA`, `JPN`)

```typescript
// Good
interface Address {
  countryCode: string;  // ISO 3166-1 alpha-2 (예: "KR")
  regionCode: string;   // ISO 3166-2 (예: "KR-11")
}

// Bad
interface Address {
  country: string;  // "대한민국" 또는 "South Korea" — 문자열 직접 저장 금지
}
```

### 2. 지역/행정구역에는 ISO 3166-2 코드를 사용한다

- 지역을 식별하는 필드에 ISO 3166-2 코드를 사용한다 (예: `KR-11`, `US-CA`)
- 코드 형식: `{alpha-2}-{subdivision}` (국가코드 + 하이픈 + 행정구역코드)
- 지역 목록은 선택된 국가에 따라 `iso_3166_2_regions.json`에서 필터링

### 3. 전화번호는 E.164 형식을 따른다

- 국제 전화번호 형식: `+{국가번호}{번호}` (예: `+821012345678`)
- 국가번호는 `country_calling_codes.json`에서 조회
- 입력 UI에서는 국가 선택과 번호 입력을 분리하여 구성
- 저장 시에는 E.164 전체 형식으로 통합 저장

```typescript
// Good: 국가코드와 번호를 분리 입력, E.164로 통합 저장
const phoneNumber = `${callingCode}${localNumber.replace(/[^0-9]/g, '')}`;
// 예: "+82" + "1012345678" → "+821012345678"

// Bad: 자유 입력 텍스트
const phone = userInput; // 형식 보장 불가
```

### 4. 국가/지역 선택 UI 데이터는 표준 JSON에서 구성한다

**국가 선택 드롭다운**:
- `iso_3166_1_countries.json`에서 목록 구성
- 표시: 한글명 또는 영문명 (로케일에 따라)
- 값: alpha-2 코드
- 주요 국가를 상단에 배치 (한국, 미국, 일본, 중국 등)

**지역 선택 드롭다운**:
- `iso_3166_2_regions.json`에서 선택된 국가의 행정구역 필터링
- 표시: 현지어명 (`nameLocal`) 또는 영문명 (`nameEn`)
- 값: ISO 3166-2 코드 (예: `KR-11`)
- 국가 선택 시 연동하여 지역 목록 갱신

**전화번호 국가코드 선택**:
- `country_calling_codes.json`에서 목록 구성
- 표시: `국기 + 국가명 + 국번` (예: `🇰🇷 대한민국 +82`)
- 값: callingCode (예: `+82`)
- alpha-2 코드를 함께 저장하여 국가 식별

### 5. 데이터 모델 필드 규칙

| 필드 용도 | 권장 필드명 | 타입 | 설명 |
|---|---|---|---|
| 국가코드 | `countryCode` / `NATN_CD` | CHAR(2) | ISO 3166-1 alpha-2 |
| 국가코드(3자리) | `countryAlpha3` / `NATN_A3_CD` | CHAR(3) | ISO 3166-1 alpha-3 |
| 지역코드 | `regionCode` / `RGN_CD` | VARCHAR(6) | ISO 3166-2 (예: KR-11) |
| 국제전화번호 | `phoneNumber` / `INTL_TELNO` | VARCHAR(15) | E.164 전체번호 |
| 국가번호 | `callingCode` / `NATN_TELNO` | VARCHAR(5) | 국제 전화 국번 (예: +82) |

### 6. 언어별 구현 패턴

#### TypeScript/React

```typescript
// 국가 선택 컴포넌트 데이터 타입
interface Country {
  koreanName: string;
  englishName: string;
  alpha2: string;      // 값으로 사용
  alpha3: string;
  numeric: string;
}

// 지역 선택 데이터 타입
interface Region {
  code: string;        // ISO 3166-2 코드 (예: "KR-11")
  nameEn: string;
  nameLocal?: string;
  type: string;
}

// 전화번호 입력 데이터 타입
interface CallingCode {
  countryNameKo: string;
  alpha2: string;
  callingCode: string;  // 예: "+82"
}
```

#### Java/Spring

```java
// 국가코드 필드
@Column(name = "NATN_CD", length = 2, columnDefinition = "CHAR(2)")
private String nationCode;  // ISO 3166-1 alpha-2

// 지역코드 필드
@Column(name = "RGN_CD", length = 6)
private String regionCode;  // ISO 3166-2 (예: "KR-11")

// 국제전화번호 필드
@Column(name = "INTL_TELNO", length = 15)
private String internationalPhoneNumber;  // E.164 형식
```

#### Python/Django

```python
# 국가코드 필드
nation_code = models.CharField(
    max_length=2, verbose_name="국가코드"
)  # ISO 3166-1 alpha-2

# 지역코드 필드
region_code = models.CharField(
    max_length=6, verbose_name="지역코드"
)  # ISO 3166-2

# 국제전화번호 필드
intl_phone_number = models.CharField(
    max_length=15, verbose_name="국제전화번호"
)  # E.164 형식
```
