---
description: 국가 선택, 지역 선택, 전화번호 입력 등 국제 표준 코드 기반 컴포넌트 코드를 생성합니다
---

# 국제 표준 코드 컴포넌트 생성

$ARGUMENTS 를 바탕으로 국제 표준 코드를 활용하는 컴포넌트 코드를 생성하라.

## 입력 형식 예시

다양한 형태의 입력을 처리한다:
- "국가 선택 드롭다운" / "country selector"
- "지역 선택 (한국)" / "region selector for KR"
- "전화번호 입력 (국가번호 포함)" / "phone input with country code"
- "국제 주소 입력 폼" / "international address form"
- "국가/지역/전화번호 통합 폼"

## 생성 절차

### 1단계: 컴포넌트 유형 판별

입력을 분석하여 생성할 컴포넌트를 결정한다:

| 키워드 | 컴포넌트 유형 | 참조 데이터 |
|---|---|---|
| 국가, country | 국가 선택기 | `data/iso_3166_1_countries.json` |
| 지역, 시/도, region, state | 지역 선택기 | `data/iso_3166_2_regions.json` |
| 전화, phone, tel, 국번, calling | 전화번호 입력 | `data/country_calling_codes.json` |
| 주소, address | 주소 입력 폼 | 위 3개 모두 |

### 2단계: 언어/프레임워크 판별

프로젝트의 주요 언어와 프레임워크를 자동 판별한다.
불분명하면 TypeScript/React를 기본으로 생성하고, 다른 언어도 함께 제공한다.

| 언어 | 프레임워크 | 생성 대상 |
|---|---|---|
| TypeScript | React | 함수형 컴포넌트 + 훅 + 타입 정의 |
| TypeScript | Vue | Composition API 컴포넌트 |
| Java | Spring Boot | REST Controller + Service + DTO |
| Python | FastAPI | Router + Schema + Service |
| Python | Django | View + Form + Template |

### 3단계: 코드 생성

#### 국가 선택기 생성 규칙

1. `iso_3166_1_countries.json` 전체를 정적 데이터로 로드
2. 독립국 필터링 옵션 제공 (`independent: true`)
3. 주요 국가를 상단에 배치 (KR, US, JP, CN, GB, DE)
4. 검색/필터 기능 포함 (한글명, 영문명으로 검색)
5. 값은 alpha-2 코드, 표시는 로케일에 따라 한글명/영문명
6. 접근성 (ARIA 속성) 고려

#### 지역 선택기 생성 규칙

1. 선택된 국가코드에 따라 `iso_3166_2_regions.json`에서 행정구역 필터링
2. 국가 선택기와 연동 — 국가 변경 시 지역 목록 자동 갱신
3. 데이터에 없는 국가는 자유 입력 텍스트로 전환
4. 표시는 현지어명(`nameLocal`) 우선, 없으면 영문명(`nameEn`)
5. 값은 ISO 3166-2 전체 코드 (예: `KR-11`)

#### 전화번호 입력기 생성 규칙

1. 국가 선택(국번) + 로컬번호 입력을 분리 구성
2. `country_calling_codes.json`에서 국번 목록 구성
3. 국가 선택 시 국번 자동 설정
4. 로컬번호에서 선행 0을 제거하여 E.164 형식으로 변환
5. E.164 전체 번호로 통합 저장: `+{국번}{번호}` (최대 15자리)
6. 유효성 검증: `^\+[1-9]\d{1,14}$`
7. 표시 형식: `+82 10-1234-5678` (로케일별 포맷팅)

#### 주소 입력 폼 생성 규칙

1. 국가 선택 → 지역 선택 → 기본주소 → 상세주소 순서로 구성
2. 국가/지역 선택기는 위 규칙 적용
3. 우편번호 필드 포함 (형식은 국가별로 다름)
4. 국가 변경 시 지역 선택과 주소 필드 초기화
5. 결과 데이터 모델:
   ```
   countryCode: CHAR(2)     — ISO 3166-1 alpha-2
   regionCode: VARCHAR(6)   — ISO 3166-2
   postalCode: VARCHAR(10)  — 우편번호
   addressLine1: VARCHAR(200) — 기본 주소
   addressLine2: VARCHAR(200) — 상세 주소
   ```

### 4단계: 타입/DTO 정의 생성

생성하는 컴포넌트에 필요한 타입 정의와 DTO를 함께 제공한다.

#### TypeScript 타입

```typescript
interface Country {
  koreanName: string;
  englishName: string;
  alpha2: string;
  alpha3: string;
  numeric: string;
  independent: boolean;
}

interface Region {
  code: string;
  nameEn: string;
  nameLocal?: string;
  type: string;
}

interface CallingCode {
  countryNameEn: string;
  countryNameKo: string;
  alpha2: string;
  callingCode: string;
  zone: number;
}
```

#### Java DTO

```java
public record CountryDto(
    String koreanName,
    String englishName,
    String alpha2,
    String alpha3,
    String numeric,
    boolean independent
) {}

public record RegionDto(
    String code,
    String nameEn,
    String nameLocal,
    String type
) {}

public record CallingCodeDto(
    String countryNameEn,
    String countryNameKo,
    String alpha2,
    String callingCode,
    int zone
) {}
```

#### Python Schema (Pydantic)

```python
from pydantic import BaseModel

class CountrySchema(BaseModel):
    korean_name: str
    english_name: str
    alpha2: str
    alpha3: str
    numeric: str
    independent: bool

class RegionSchema(BaseModel):
    code: str
    name_en: str
    name_local: str | None = None
    type: str

class CallingCodeSchema(BaseModel):
    country_name_en: str
    country_name_ko: str
    alpha2: str
    calling_code: str
    zone: int
```

### 5단계: DB 스키마 제안 (필요 시)

국제 코드 데이터를 DB에 저장하는 경우, 표준 네이밍 규칙에 따른 DDL을 제안한다.

```sql
-- 국가 마스터 테이블
CREATE TABLE TC_NATN_CD (
    NATN_CD       CHAR(2)      NOT NULL COMMENT '국가코드 (ISO 3166-1 alpha-2)',
    NATN_A3_CD    CHAR(3)      NOT NULL COMMENT '국가코드3자리 (ISO 3166-1 alpha-3)',
    NATN_NO       CHAR(3)      NOT NULL COMMENT '국가숫자코드 (ISO 3166-1 numeric)',
    NATN_KO_NM    VARCHAR(50)  NOT NULL COMMENT '국가한글명',
    NATN_EN_NM    VARCHAR(100) NOT NULL COMMENT '국가영문명',
    INDP_YN       CHAR(1)      NOT NULL DEFAULT 'Y' COMMENT '독립국여부',
    NATN_TELNO    VARCHAR(5)   COMMENT '국가전화번호 (국제전화 국번)',
    USE_YN        CHAR(1)      NOT NULL DEFAULT 'Y' COMMENT '사용여부',
    REG_DT        DATETIME     NOT NULL COMMENT '등록일시',
    CHG_DT        DATETIME     COMMENT '변경일시',
    PRIMARY KEY (NATN_CD)
) COMMENT '국가코드';

-- 지역 마스터 테이블
CREATE TABLE TC_RGN_CD (
    RGN_CD        VARCHAR(6)   NOT NULL COMMENT '지역코드 (ISO 3166-2)',
    NATN_CD       CHAR(2)      NOT NULL COMMENT '국가코드',
    RGN_EN_NM     VARCHAR(100) NOT NULL COMMENT '지역영문명',
    RGN_LCL_NM    VARCHAR(100) COMMENT '지역현지어명',
    RGN_TYPE_NM   VARCHAR(50)  NOT NULL COMMENT '지역유형명',
    USE_YN        CHAR(1)      NOT NULL DEFAULT 'Y' COMMENT '사용여부',
    REG_DT        DATETIME     NOT NULL COMMENT '등록일시',
    CHG_DT        DATETIME     COMMENT '변경일시',
    PRIMARY KEY (RGN_CD),
    FOREIGN KEY (NATN_CD) REFERENCES TC_NATN_CD(NATN_CD)
) COMMENT '지역코드';
```

## 추가 규칙

- 코딩 컨벤션 스킬의 언어별 규칙을 준수한다 (Java: Google Style, TypeScript: Google TS Style, Python: PEP 8)
- 데이터 표준 스킬의 DB 네이밍 규칙을 준수한다 (표준 약어, 접미사 패턴, 테이블 접두사)
- JSON 데이터 파일을 직접 수정하지 말 것 — 읽기 전용으로 참조만 한다
- 접근성(a11y)을 고려한다: ARIA 레이블, 키보드 네비게이션, 스크린 리더 지원
- 국제화(i18n)를 고려한다: 표시명은 로케일에 따라 한글/영문 전환
