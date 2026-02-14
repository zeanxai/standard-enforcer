# 국제 표준 코드 상세 참조 가이드

## 1. ISO 3166-1 국가 코드

### 개요
ISO 3166-1은 국가 및 종속 영토를 식별하기 위한 국제 표준이다.

### 데이터 파일
`data/iso_3166_1_countries.json` — 249개 항목

### 스키마
```json
{
  "koreanName": "대한민국",
  "englishName": "Korea (the Republic of)",
  "alpha2": "KR",
  "alpha3": "KOR",
  "numeric": "410",
  "independent": true
}
```

| 필드 | 설명 | 예시 |
|---|---|---|
| `koreanName` | 한글 국가명 | 대한민국, 미국, 일본 |
| `englishName` | ISO 공식 영문명 | Korea (the Republic of) |
| `alpha2` | 2자리 알파벳 코드 | KR, US, JP |
| `alpha3` | 3자리 알파벳 코드 | KOR, USA, JPN |
| `numeric` | 3자리 숫자 코드 (0 패딩) | 410, 840, 392 |
| `independent` | 독립국 여부 | true/false |

### 주요 국가 코드 (빈번 사용)

| 한글명 | alpha-2 | alpha-3 | numeric | 국번 |
|---|---|---|---|---|
| 대한민국 | KR | KOR | 410 | +82 |
| 미국 | US | USA | 840 | +1 |
| 일본 | JP | JPN | 392 | +81 |
| 중국 | CN | CHN | 156 | +86 |
| 영국 | GB | GBR | 826 | +44 |
| 독일 | DE | DEU | 276 | +49 |
| 프랑스 | FR | FRA | 250 | +33 |
| 캐나다 | CA | CAN | 124 | +1 |
| 호주 | AU | AUS | 036 | +61 |
| 인도 | IN | IND | 356 | +91 |
| 싱가포르 | SG | SGP | 702 | +65 |
| 대만 | TW | TWN | 158 | +886 |
| 태국 | TH | THA | 764 | +66 |
| 베트남 | VN | VNM | 704 | +84 |
| 필리핀 | PH | PHL | 608 | +63 |

### 사용 규칙

1. **alpha-2를 기본 식별자로 사용**: DB 저장, API 파라미터, 내부 로직 모두 alpha-2 사용
2. **alpha-3는 표시/교환용**: ISO 표준 문서 교환, 공식 보고서에 사용
3. **numeric은 알파벳 독립이 필요할 때**: 다국어 환경에서 문자 체계에 무관한 식별이 필요할 때
4. **`independent` 필터링**: 드롭다운에서 독립국만 표시할 때 `independent: true`로 필터

---

## 2. ISO 3166-2 지역/행정구역 코드

### 개요
ISO 3166-2는 각 국가의 행정구역(시/도/주/현 등)을 식별하기 위한 국제 표준이다.

### 데이터 파일
`data/iso_3166_2_regions.json` — 21개국, 653개 행정구역

### 스키마
```json
{
  "KR": {
    "countryNameEn": "South Korea",
    "countryNameKo": "대한민국",
    "alpha2": "KR",
    "subdivisionCount": 17,
    "subdivisions": [
      {
        "code": "KR-11",
        "nameEn": "Seoul",
        "nameLocal": "서울특별시",
        "type": "Special city"
      }
    ]
  }
}
```

| 필드 | 설명 | 예시 |
|---|---|---|
| `code` | ISO 3166-2 코드 (국가-구역) | KR-11, US-CA, JP-13 |
| `nameEn` | 영문 지역명 | Seoul, California, Tokyo |
| `nameLocal` | 현지어 지역명 (있는 경우) | 서울특별시, 東京都 |
| `type` | 행정구역 유형 | Special city, State, Prefecture |

### 수록 국가

| 국가 | alpha-2 | 행정구역 수 | 구역 유형 |
|---|---|---|---|
| 대한민국 | KR | 17 | 특별시, 광역시, 도, 특별자치시/도 |
| 미국 | US | 57 | State, District, Territory |
| 일본 | JP | 47 | Prefecture (都道府県) |
| 중국 | CN | 34 | Province, Municipality, Autonomous Region, SAR |
| 영국 | GB | 4 | Country (England, Scotland, Wales, NI) |
| 독일 | DE | 16 | Land (Bundesland) |
| 프랑스 | FR | 18 | Region (Région) |
| 캐나다 | CA | 13 | Province, Territory |
| 호주 | AU | 8 | State, Territory |
| 인도 | IN | 36 | State, Union Territory |
| 브라질 | BR | 27 | State, Federal District |
| 이탈리아 | IT | 20 | Region (Regione) |
| 스페인 | ES | 19 | Autonomous Community, City |
| 러시아 | RU | 85 | Oblast, Republic, Krai, City, Autonomous |
| 멕시코 | MX | 32 | State, Federal District |
| 인도네시아 | ID | 38 | Province (Provinsi) |
| 태국 | TH | 77 | Province (Changwat) |
| 베트남 | VN | 63 | Province, Municipality |
| 필리핀 | PH | 82 | Province, City, District |
| 싱가포르 | SG | 5 | District |
| 대만 | TW | 22 | City, County |

### 사용 규칙

1. **국가 선택에 연동**: 국가 선택 시 해당 국가의 행정구역 목록을 동적 로드
2. **코드 형식 보존**: `KR-11` 전체를 저장 (국가코드 + 하이픈 + 구역코드)
3. **표시명 로케일 대응**: 한국어 UI에서는 `nameLocal`, 영문 UI에서는 `nameEn` 사용
4. **미지원 국가 대비**: 데이터에 없는 국가는 지역 선택을 숨기거나 자유 입력으로 전환

---

## 3. ITU-T E.164 국제 전화 국번

### 개요
ITU-T E.164는 국제 공중 전화 번호 체계를 정의하는 국제 표준이다.

### 데이터 파일
`data/country_calling_codes.json` — 245개 항목

### 스키마
```json
{
  "countryNameEn": "South Korea",
  "countryNameKo": "대한민국",
  "alpha2": "KR",
  "callingCode": "+82",
  "zone": 8
}
```

공유 코드가 있는 경우:
```json
{
  "countryNameEn": "United States",
  "countryNameKo": "미국",
  "alpha2": "US",
  "callingCode": "+1",
  "zone": 1,
  "sharedWith": ["CA", "PR", "VI", "GU", "AS", "MP"]
}
```

| 필드 | 설명 | 예시 |
|---|---|---|
| `countryNameEn` | 영문 국가명 | South Korea |
| `countryNameKo` | 한글 국가명 | 대한민국 |
| `alpha2` | ISO 3166-1 alpha-2 | KR |
| `callingCode` | 국제 전화 국번 (+ 포함) | +82 |
| `zone` | ITU 존 번호 (1~9) | 8 |
| `sharedWith` | 동일 국번 공유 국가 목록 (선택) | ["CA", "PR"] |

### ITU 존 구분

| 존 | 지역 | 대표 국번 |
|---|---|---|
| 1 | 북미 (NANP) | +1 |
| 2 | 아프리카 | +2xx |
| 3 | 유럽 남부/동부 | +3xx |
| 4 | 유럽 북부/서부 | +4x |
| 5 | 중남미 | +5xx |
| 6 | 동남아/오세아니아 | +6x |
| 7 | 러시아/CIS | +7 |
| 8 | 동아시아 | +8x |
| 9 | 서아시아/남아시아 | +9xx |

### 사용 규칙

1. **E.164 전체 형식으로 저장**: `+국번 + 로컬번호` (공백/하이픈 없이). 최대 15자리
2. **국번과 로컬번호 분리 입력**: UI에서는 국가 선택(국번 자동 설정) + 로컬번호 입력
3. **국번 표시 형식**: `+82` 형태로 표시 (+ 기호 포함)
4. **공유 코드 주의**: +1은 미국/캐나다 등 NANP 국가가 공유. alpha-2로 국가를 구분

---

## 4. 컴포넌트 구현 패턴

### 4.1 국가 선택 (Country Selector)

#### TypeScript/React

```tsx
import countriesData from '@/data/iso_3166_1_countries.json';

interface CountryOption {
  value: string;   // alpha-2
  label: string;   // 표시명
  alpha3: string;
  numeric: string;
}

// 주요 국가 우선 정렬
const PRIORITY_COUNTRIES = ['KR', 'US', 'JP', 'CN', 'GB', 'DE'];

export function getCountryOptions(locale: 'ko' | 'en' = 'ko'): CountryOption[] {
  const countries = countriesData
    .filter((c) => c.independent)
    .map((c) => ({
      value: c.alpha2,
      label: locale === 'ko' ? c.koreanName : c.englishName,
      alpha3: c.alpha3,
      numeric: c.numeric,
    }));

  const priority = countries.filter((c) => PRIORITY_COUNTRIES.includes(c.value));
  const rest = countries
    .filter((c) => !PRIORITY_COUNTRIES.includes(c.value))
    .sort((a, b) => a.label.localeCompare(b.label, locale));

  return [...priority, ...rest];
}
```

#### Java/Spring

```java
@Service
public class CountryService {

  private List<CountryDto> countries;

  @PostConstruct
  public void init() {
    ObjectMapper mapper = new ObjectMapper();
    InputStream is = getClass().getResourceAsStream("/data/iso_3166_1_countries.json");
    countries = mapper.readValue(is, new TypeReference<List<CountryDto>>() {});
  }

  public List<CountryDto> getCountries(boolean independentOnly) {
    return countries.stream()
        .filter(c -> !independentOnly || c.isIndependent())
        .sorted(Comparator.comparing(CountryDto::getKoreanName))
        .collect(Collectors.toList());
  }

  public Optional<CountryDto> findByAlpha2(String alpha2) {
    return countries.stream()
        .filter(c -> c.getAlpha2().equalsIgnoreCase(alpha2))
        .findFirst();
  }
}
```

#### Python/FastAPI

```python
import json
from pathlib import Path
from functools import lru_cache

@lru_cache
def load_countries() -> list[dict]:
    path = Path(__file__).parent / "data" / "iso_3166_1_countries.json"
    with open(path, encoding="utf-8") as f:
        return json.load(f)

def get_country_options(independent_only: bool = True, locale: str = "ko") -> list[dict]:
    countries = load_countries()
    if independent_only:
        countries = [c for c in countries if c["independent"]]
    name_key = "koreanName" if locale == "ko" else "englishName"
    return [
        {"value": c["alpha2"], "label": c[name_key]}
        for c in sorted(countries, key=lambda c: c[name_key])
    ]
```

### 4.2 지역 선택 (Region Selector)

#### TypeScript/React

```tsx
import regionsData from '@/data/iso_3166_2_regions.json';

interface RegionOption {
  value: string;   // ISO 3166-2 코드 (예: "KR-11")
  label: string;   // 표시명
  type: string;    // 행정구역 유형
}

export function getRegionOptions(
  countryCode: string,
  locale: 'ko' | 'en' = 'ko',
): RegionOption[] {
  const country = regionsData[countryCode as keyof typeof regionsData];
  if (!country) {
    return [];
  }

  return country.subdivisions.map((s) => ({
    value: s.code,
    label: locale === 'ko' && s.nameLocal ? s.nameLocal : s.nameEn,
    type: s.type,
  }));
}
```

#### Java/Spring

```java
@Service
public class RegionService {

  private Map<String, RegionCountryDto> regions;

  @PostConstruct
  public void init() {
    ObjectMapper mapper = new ObjectMapper();
    InputStream is = getClass().getResourceAsStream("/data/iso_3166_2_regions.json");
    regions = mapper.readValue(is, new TypeReference<Map<String, RegionCountryDto>>() {});
  }

  public List<SubdivisionDto> getRegions(String countryCode) {
    RegionCountryDto country = regions.get(countryCode.toUpperCase());
    if (country == null) {
      return Collections.emptyList();
    }
    return country.getSubdivisions();
  }
}
```

#### Python/FastAPI

```python
import json
from pathlib import Path
from functools import lru_cache

@lru_cache
def load_regions() -> dict:
    path = Path(__file__).parent / "data" / "iso_3166_2_regions.json"
    with open(path, encoding="utf-8") as f:
        return json.load(f)

def get_region_options(country_code: str, locale: str = "ko") -> list[dict]:
    regions = load_regions()
    country = regions.get(country_code.upper())
    if not country:
        return []
    name_key = "nameLocal" if locale == "ko" else "nameEn"
    return [
        {
            "value": s["code"],
            "label": s.get(name_key) or s["nameEn"],
            "type": s["type"],
        }
        for s in country["subdivisions"]
    ]
```

### 4.3 전화번호 입력 (Phone Number Input)

#### TypeScript/React

```tsx
import callingCodesData from '@/data/country_calling_codes.json';

interface CallingCodeOption {
  value: string;       // callingCode (예: "+82")
  label: string;       // 표시명
  alpha2: string;      // 국가코드
  countryName: string; // 국가명
}

const PRIORITY_COUNTRIES = ['KR', 'US', 'JP', 'CN'];

export function getCallingCodeOptions(locale: 'ko' | 'en' = 'ko'): CallingCodeOption[] {
  const codes = callingCodesData.map((c) => ({
    value: c.callingCode,
    label: `${c.callingCode} ${locale === 'ko' ? c.countryNameKo : c.countryNameEn}`,
    alpha2: c.alpha2,
    countryName: locale === 'ko' ? c.countryNameKo : c.countryNameEn,
  }));

  const priority = codes.filter((c) => PRIORITY_COUNTRIES.includes(c.alpha2));
  const rest = codes
    .filter((c) => !PRIORITY_COUNTRIES.includes(c.alpha2))
    .sort((a, b) => a.countryName.localeCompare(b.countryName, locale));

  return [...priority, ...rest];
}

// E.164 전화번호 조합
export function toE164(callingCode: string, localNumber: string): string {
  const digits = localNumber.replace(/[^0-9]/g, '');
  // 선행 0 제거 (국내 번호 형식에서 국제 형식으로 변환)
  const normalized = digits.startsWith('0') ? digits.slice(1) : digits;
  return `${callingCode}${normalized}`;
}

// E.164 전화번호 파싱
export function parseE164(
  e164: string,
): { callingCode: string; localNumber: string } | null {
  const match = callingCodesData
    .map((c) => c.callingCode)
    .sort((a, b) => b.length - a.length) // 긴 코드부터 매칭
    .find((code) => e164.startsWith(code));

  if (!match) {
    return null;
  }

  return {
    callingCode: match,
    localNumber: e164.slice(match.length),
  };
}
```

#### Java/Spring

```java
@Service
public class PhoneNumberService {

  private List<CallingCodeDto> callingCodes;

  @PostConstruct
  public void init() {
    ObjectMapper mapper = new ObjectMapper();
    InputStream is = getClass().getResourceAsStream("/data/country_calling_codes.json");
    callingCodes = mapper.readValue(is, new TypeReference<List<CallingCodeDto>>() {});
  }

  public String toE164(String callingCode, String localNumber) {
    String digits = localNumber.replaceAll("[^0-9]", "");
    if (digits.startsWith("0")) {
      digits = digits.substring(1);
    }
    return callingCode + digits;
  }

  public Optional<CallingCodeDto> findByAlpha2(String alpha2) {
    return callingCodes.stream()
        .filter(c -> c.getAlpha2().equalsIgnoreCase(alpha2))
        .findFirst();
  }

  public List<CallingCodeDto> getCallingCodes() {
    return Collections.unmodifiableList(callingCodes);
  }
}
```

#### Python/FastAPI

```python
import re
import json
from pathlib import Path
from functools import lru_cache

@lru_cache
def load_calling_codes() -> list[dict]:
    path = Path(__file__).parent / "data" / "country_calling_codes.json"
    with open(path, encoding="utf-8") as f:
        return json.load(f)

def to_e164(calling_code: str, local_number: str) -> str:
    digits = re.sub(r"[^0-9]", "", local_number)
    if digits.startswith("0"):
        digits = digits[1:]
    return f"{calling_code}{digits}"

def parse_e164(e164: str) -> dict | None:
    codes = sorted(
        [c["callingCode"] for c in load_calling_codes()],
        key=len,
        reverse=True,
    )
    for code in codes:
        if e164.startswith(code):
            return {"callingCode": code, "localNumber": e164[len(code):]}
    return None
```

### 4.4 주소 입력 폼 (Address Form)

국가 → 지역 → 상세주소 연동 패턴:

```typescript
// 주소 데이터 모델
interface InternationalAddress {
  countryCode: string;     // ISO 3166-1 alpha-2 (예: "KR")
  regionCode: string;      // ISO 3166-2 (예: "KR-11")
  addressLine1: string;    // 기본 주소
  addressLine2?: string;   // 상세 주소
  postalCode?: string;     // 우편번호
}

// 국가 변경 시 지역 목록 갱신
function onCountryChange(countryCode: string): void {
  const regions = getRegionOptions(countryCode);
  // 지역 선택 드롭다운 갱신
  // 이전 지역 선택 초기화
}
```

---

## 5. DB 컬럼 매핑 규칙

국제 표준 코드 관련 DB 컬럼은 공공 데이터 표준과 함께 아래 규칙을 따른다:

| 논리명 | 물리 컬럼명 | 타입 | 참조 표준 |
|---|---|---|---|
| 국가코드 | NATN_CD | CHAR(2) | ISO 3166-1 alpha-2 |
| 국가코드(3자리) | NATN_A3_CD | CHAR(3) | ISO 3166-1 alpha-3 |
| 국가숫자코드 | NATN_NO | CHAR(3) | ISO 3166-1 numeric |
| 지역코드 | RGN_CD | VARCHAR(6) | ISO 3166-2 |
| 국제전화번호 | INTL_TELNO | VARCHAR(15) | E.164 |
| 국가전화번호 | NATN_TELNO | VARCHAR(5) | ITU-T 국번 |
| 국가명(한글) | NATN_KO_NM | VARCHAR(50) | iso_3166_1_countries.json |
| 국가명(영문) | NATN_EN_NM | VARCHAR(100) | iso_3166_1_countries.json |
| 지역명(현지어) | RGN_LCL_NM | VARCHAR(100) | iso_3166_2_regions.json |
| 지역명(영문) | RGN_EN_NM | VARCHAR(100) | iso_3166_2_regions.json |

### JPA 엔티티 예시

```java
@Entity
@Table(name = "TB_INTL_ADDR")
public class InternationalAddress {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(name = "INTL_ADDR_SN")
  private Long internationalAddressSn;

  @Column(name = "NATN_CD", length = 2, nullable = false,
      columnDefinition = "CHAR(2)")
  private String nationCode;

  @Column(name = "RGN_CD", length = 6)
  private String regionCode;

  @Column(name = "INTL_TELNO", length = 15)
  private String internationalPhoneNumber;

  @Column(name = "NATN_TELNO", length = 5)
  private String nationPhoneNumber;

  @Column(name = "ADDR1", length = 200, nullable = false)
  private String addressLine1;

  @Column(name = "ADDR2", length = 200)
  private String addressLine2;

  @Column(name = "REG_DT")
  private LocalDateTime regDt;

  @Column(name = "CHG_DT")
  private LocalDateTime chgDt;
}
```

---

## 6. 유효성 검증 규칙

### 국가코드 검증
- alpha-2: 정확히 2자리 대문자 알파벳, `iso_3166_1_countries.json`에 존재하는지 확인
- alpha-3: 정확히 3자리 대문자 알파벳

### 지역코드 검증
- 형식: `{alpha-2}-{구역코드}` (예: `KR-11`, `US-CA`)
- 국가코드 부분이 유효한 ISO 3166-1 alpha-2인지 확인
- 해당 국가의 `iso_3166_2_regions.json`에 코드가 존재하는지 확인

### 전화번호 검증
- E.164 형식: `+` 로 시작, 최대 15자리 숫자
- 정규식: `^\+[1-9]\d{1,14}$`
- 국번이 `country_calling_codes.json`에 존재하는지 확인

```typescript
// 유효성 검증 함수
export function isValidCountryCode(code: string): boolean {
  return /^[A-Z]{2}$/.test(code)
    && countriesData.some((c) => c.alpha2 === code);
}

export function isValidRegionCode(code: string): boolean {
  const [country] = code.split('-');
  const regionData = regionsData[country as keyof typeof regionsData];
  return regionData?.subdivisions.some((s) => s.code === code) ?? false;
}

export function isValidE164(phone: string): boolean {
  return /^\+[1-9]\d{1,14}$/.test(phone);
}
```
