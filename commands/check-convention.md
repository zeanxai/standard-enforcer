---
description: 코드의 코딩 컨벤션 준수 여부를 검사하고 위반 사항을 보고합니다
---

# 코딩 컨벤션 검사

$ARGUMENTS 에 대해 코딩 컨벤션 준수 여부를 검사하라.

## 검사 절차

1. 대상 파일을 읽고 확장자로 언어를 판별한다 (`.java` → Java, `.ts`/`.tsx` → TypeScript, `.py` → Python)
2. 해당 언어의 코딩 컨벤션 참조 문서를 확인한다
3. 아래 항목을 순서대로 검사한다

## 검사 항목

### 공통
- [ ] 파일 인코딩 (UTF-8)
- [ ] 줄 길이 제한 준수 (Java: 100자, TypeScript: Prettier, Python: 79자)
- [ ] 들여쓰기 규칙 (Java: 2스페이스, Python: 4스페이스)
- [ ] import 순서 및 와일드카드 사용 여부
- [ ] 네이밍 컨벤션 (클래스, 메서드, 변수, 상수)

### Java 전용
- [ ] K&R 중괄호 스타일 (여는 중괄호 같은 줄)
- [ ] 단일 문장 본문에도 중괄호 사용
- [ ] @Override 어노테이션 사용 여부
- [ ] Javadoc 작성 여부 (public/protected 멤버)
- [ ] static 멤버 접근 방식 (클래스명으로만)
- [ ] 배열 선언 스타일 (`String[] args`)
- [ ] long 리터럴 대문자 L 접미사

### TypeScript 전용
- [ ] `export default` 사용 여부 (금지)
- [ ] `any` 타입 사용 여부 (금지 → `unknown`)
- [ ] `var` 사용 여부 (금지 → `const`/`let`)
- [ ] `.forEach()` 사용 여부 (금지 → `for...of`)
- [ ] `const enum` 사용 여부 (금지)
- [ ] `===` / `!==` 사용 여부 (`==`/`!=` 금지, `== null` 예외)
- [ ] 세미콜론 누락 여부
- [ ] `namespace` 사용 여부 (금지)

### Python 전용
- [ ] PEP 8 네이밍 (snake_case 함수/변수, CapWords 클래스)
- [ ] `is None` / `is not None` 사용 여부 (`== None` 금지)
- [ ] `isinstance()` 사용 여부 (`type()` 비교 금지)
- [ ] `with` 문 사용 여부 (리소스 관리)
- [ ] 빈 시퀀스 체크 (`if not seq:` 사용, `if len(seq):` 금지)
- [ ] bare `except:` 사용 여부 (금지 → 구체적 예외 타입)
- [ ] lambda 할당 여부 (금지)

## 출력 형식

위반 사항을 아래 형식으로 보고하라:

| 심각도 | 파일:라인 | 규칙 | 현재 코드 | 수정 제안 |
|---|---|---|---|---|
| Error | ... | ... | ... | ... |
| Warning | ... | ... | ... | ... |
| Info | ... | ... | ... | ... |

심각도 기준:
- **Error**: 금지 패턴 위반 (var, any, export default, == None, bare except 등)
- **Warning**: 컨벤션 불일치 (네이밍, 줄 길이, 들여쓰기 등)
- **Info**: 개선 권장 (Javadoc 누락, 독스트링 미작성 등)

마지막에 총 위반 건수와 심각도별 요약을 보고하라.
