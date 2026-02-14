# Standard Enforcer 플러그인 배포 가이드

> Claude Code 플러그인을 다른 사용자가 설치하여 사용할 수 있도록 배포하는 방법

---

## 1. 배포 방식 개요

Claude Code 플러그인은 npm이 아닌 **마켓플레이스(Marketplace)** 시스템을 통해 배포된다. 마켓플레이스는 플러그인 목록을 관리하는 Git 저장소이다.

| 배포 방식 | 대상 | 난이도 | 설명 |
|---|---|---|---|
| **로컬 테스트** | 본인만 | 즉시 | `--plugin-dir` 플래그로 로컬 디렉토리 직접 로드 |
| **자체 마켓플레이스** | 팀/조직 | 낮음 | GitHub 저장소에 마켓플레이스를 만들어 배포 |
| **프로젝트 설정 배포** | 프로젝트 팀원 | 낮음 | `.claude/settings.json`에 마켓플레이스를 등록하여 자동 설치 유도 |
| **공식 디렉토리 등록** | 전체 사용자 | 중간 | Anthropic 공식 플러그인 디렉토리에 제출 |

---

## 2. 배포 전 준비사항

### 2.1 플러그인 매니페스트 확인

`.claude-plugin/plugin.json`이 올바르게 구성되어 있어야 한다.

```json
{
  "name": "standard-enforcer",
  "version": "1.0.0",
  "description": "코딩 컨벤션(Java/TypeScript/Python)과 공공 데이터 표준 용어 사전을 기반으로 일관된 코드 작성을 지원하는 플러그인",
  "author": {
    "name": "Team",
    "email": "dev@example.com",
    "url": "https://github.com/your-org"
  },
  "homepage": "https://github.com/your-org/standard-enforcer",
  "repository": "https://github.com/your-org/standard-enforcer",
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

**필수 필드**: `name` (kebab-case, 공백 불가)
**권장 필드**: `version`, `description`, `author`, `license`, `keywords`

### 2.2 디렉토리 구조 점검

배포 시 플러그인 디렉토리가 캐시에 복사되므로, 모든 경로가 상대 경로여야 한다.

```
standard-enforcer/
├── .claude-plugin/
│   └── plugin.json           # 매니페스트 (이 디렉토리에만 위치)
├── commands/                  # 슬래시 커맨드 (플러그인 루트에 위치)
├── skills/                    # 스킬 정의 (플러그인 루트에 위치)
├── agents/                    # 서브에이전트 (플러그인 루트에 위치)
├── hooks/
│   └── hooks.json             # 훅 설정
├── scripts/                   # 스크립트
├── data/                      # 데이터 파일
├── README.md
├── LICENSE
└── CHANGELOG.md
```

**주의**: `commands/`, `agents/`, `skills/`, `hooks/` 디렉토리는 반드시 **플러그인 루트**에 위치해야 한다. `.claude-plugin/` 안에 넣으면 안 된다.

### 2.3 경로 참조 시 `${CLAUDE_PLUGIN_ROOT}` 사용

플러그인이 설치되면 캐시 디렉토리에 복사되므로, 훅이나 스크립트에서 절대 경로 대신 반드시 `${CLAUDE_PLUGIN_ROOT}` 환경변수를 사용해야 한다.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/check-forbidden-words.sh"
          }
        ]
      }
    ]
  }
}
```

### 2.4 플러그인 유효성 검증

```bash
claude plugin validate .
```

배포 전 반드시 실행하여 구조적 오류가 없는지 확인한다.

### 2.5 로컬 테스트

```bash
# 플러그인을 로컬에서 로드하여 테스트
claude --plugin-dir ./standard-enforcer

# 여러 플러그인 동시 로드
claude --plugin-dir ./standard-enforcer --plugin-dir ./other-plugin

# 디버그 모드로 실행 (플러그인 로딩 과정 확인)
claude --debug
```

---

## 3. 방법 A: 자체 마켓플레이스 생성 (권장)

가장 일반적인 배포 방법이다. GitHub(또는 GitLab, Bitbucket) 저장소에 마켓플레이스를 만들고, 그 안에 플러그인을 등록한다.

### 3.1 마켓플레이스 저장소 구조

두 가지 구성 방식이 가능하다.

#### 방식 1: 마켓플레이스에 플러그인 포함 (모노레포)

```
my-marketplace/
├── .claude-plugin/
│   └── marketplace.json       # 마켓플레이스 매니페스트
├── plugins/
│   ├── standard-enforcer/     # 플러그인 디렉토리 통째로 포함
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── commands/
│   │   ├── skills/
│   │   ├── agents/
│   │   ├── hooks/
│   │   ├── scripts/
│   │   └── data/
│   └── another-plugin/
│       └── ...
└── README.md
```

#### 방식 2: 외부 저장소 참조

```
my-marketplace/
├── .claude-plugin/
│   └── marketplace.json       # 외부 GitHub 저장소를 참조
└── README.md
```

### 3.2 marketplace.json 작성

`.claude-plugin/marketplace.json` 파일을 마켓플레이스 저장소 루트에 생성한다.

#### 방식 1: 로컬 경로 참조 (모노레포)

```json
{
  "name": "astra-tools",
  "owner": {
    "name": "Astra Team",
    "email": "dev@example.com"
  },
  "metadata": {
    "description": "Astra 팀 Claude Code 플러그인 모음",
    "version": "1.0.0",
    "pluginRoot": "./plugins"
  },
  "plugins": [
    {
      "name": "standard-enforcer",
      "source": "./plugins/standard-enforcer",
      "description": "코딩 컨벤션과 공공 데이터 표준 용어 사전 기반 코드 품질 도구",
      "version": "1.0.0",
      "author": { "name": "Astra Team" },
      "license": "MIT",
      "keywords": ["coding-convention", "data-standard", "korean-standard"],
      "category": "code-quality",
      "tags": ["java", "typescript", "python"]
    }
  ]
}
```

#### 방식 2: 외부 GitHub 저장소 참조

```json
{
  "name": "astra-tools",
  "owner": {
    "name": "Astra Team",
    "email": "dev@example.com"
  },
  "metadata": {
    "description": "Astra 팀 Claude Code 플러그인 모음",
    "version": "1.0.0"
  },
  "plugins": [
    {
      "name": "standard-enforcer",
      "source": {
        "source": "github",
        "repo": "your-org/standard-enforcer",
        "ref": "v1.0.0",
        "sha": "a1b2c3d4e5f6..."
      },
      "description": "코딩 컨벤션과 공공 데이터 표준 용어 사전 기반 코드 품질 도구",
      "version": "1.0.0"
    }
  ]
}
```

#### 방식 3: Git URL 참조 (GitHub 외 호스팅)

```json
{
  "plugins": [
    {
      "name": "standard-enforcer",
      "source": {
        "source": "url",
        "url": "https://gitlab.com/your-org/standard-enforcer.git"
      },
      "description": "코딩 컨벤션과 공공 데이터 표준 용어 사전 기반 코드 품질 도구"
    }
  ]
}
```

### 3.3 마켓플레이스 저장소 생성 및 푸시

```bash
# 1. 마켓플레이스 저장소 생성
mkdir astra-claude-plugins
cd astra-claude-plugins
git init

# 2. marketplace.json 생성
mkdir -p .claude-plugin
# .claude-plugin/marketplace.json 작성 (위 예시 참조)

# 3. 플러그인 복사 (모노레포 방식인 경우)
mkdir -p plugins
cp -r /path/to/standard-enforcer plugins/

# 4. 커밋 및 푸시
git add .
git commit -m "Initial marketplace with standard-enforcer plugin"
git remote add origin https://github.com/your-org/astra-claude-plugins.git
git push -u origin main

# 5. 버전 태그 (선택)
git tag v1.0.0
git push --tags
```

### 3.4 사용자 설치 방법

마켓플레이스가 준비되면, 사용자는 다음과 같이 설치한다.

```bash
# 1단계: 마켓플레이스 등록
/plugin marketplace add your-org/astra-claude-plugins

# 또는 전체 URL로
/plugin marketplace add https://github.com/your-org/astra-claude-plugins.git

# 또는 특정 브랜치/태그 지정
/plugin marketplace add https://github.com/your-org/astra-claude-plugins.git#v1.0.0

# 2단계: 플러그인 설치
/plugin install standard-enforcer@astra-tools

# 설치 범위 지정 (선택)
/plugin install standard-enforcer@astra-tools --scope project   # 프로젝트 단위
/plugin install standard-enforcer@astra-tools --scope user      # 사용자 전역 (기본)
/plugin install standard-enforcer@astra-tools --scope local     # 로컬 전용 (gitignore)
```

---

## 4. 방법 B: 프로젝트 설정을 통한 팀 배포

프로젝트의 `.claude/settings.json`에 마켓플레이스와 플러그인을 등록하면, 팀원이 해당 프로젝트 폴더를 신뢰(trust)할 때 자동으로 설치가 안내된다.

### 4.1 프로젝트 settings.json 설정

```json
{
  "extraKnownMarketplaces": {
    "astra-tools": {
      "source": {
        "source": "github",
        "repo": "your-org/astra-claude-plugins"
      }
    }
  },
  "enabledPlugins": {
    "standard-enforcer@astra-tools": true
  }
}
```

### 4.2 동작 방식

1. 팀원이 프로젝트 폴더에서 `claude`를 실행한다
2. Claude Code가 `.claude/settings.json`을 읽는다
3. 등록된 마켓플레이스에서 플러그인 설치를 안내한다
4. 팀원이 승인하면 자동으로 설치된다

### 4.3 설치 범위 비교

| 범위 | 설정 파일 | 용도 |
|---|---|---|
| `user` | `~/.claude/settings.json` | 개인 전역 설정 |
| `project` | `.claude/settings.json` | 팀 공유 (버전 관리에 포함) |
| `local` | `.claude/settings.local.json` | 프로젝트별 개인 설정 (gitignore) |
| `managed` | `managed-settings.json` | 관리자 제어 (읽기 전용) |

---

## 5. 방법 C: Anthropic 공식 디렉토리 등록

전체 Claude Code 사용자에게 공개하려면 Anthropic 공식 플러그인 디렉토리에 등록할 수 있다.

### 5.1 공식 디렉토리 정보

- **저장소**: [github.com/anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official)
- **접수 양식**: [clau.de/plugin-directory-submission](https://clau.de/plugin-directory-submission)
- Claude Code에서 `/plugin` → "Discover" 탭에 자동 노출된다

### 5.2 등록 절차

1. 플러그인을 독립 GitHub 저장소로 공개한다
2. README.md에 사용법, 설치 방법을 명시한다
3. LICENSE 파일을 포함한다 (MIT, Apache 2.0 등)
4. [plugin directory submission form](https://clau.de/plugin-directory-submission)을 통해 제출한다
5. Anthropic 팀의 품질 및 보안 심사를 통과하면 등록된다

### 5.3 공식 등록 시 요구사항 (예상)

- 명확한 `plugin.json` 매니페스트
- README.md (설치 방법, 사용법, 기능 설명)
- LICENSE 파일
- 보안 문제가 없는 코드 (악성 스크립트 금지)
- 외부 플러그인은 `external_plugins/` 디렉토리에 등록됨

---

## 6. 비공개 저장소를 통한 배포

사내 전용 플러그인을 비공개 저장소로 배포하는 경우에도 동일한 방식으로 작동한다.

### 6.1 요구사항

사용자의 터미널에서 `git clone`이 가능하면 Claude Code에서도 작동한다.

### 6.2 인증 환경변수

백그라운드 자동 업데이트를 위해 토큰 환경변수를 설정한다.

| Git 호스팅 | 환경변수 |
|---|---|
| GitHub | `GITHUB_TOKEN` 또는 `GH_TOKEN` |
| GitLab | `GITLAB_TOKEN` 또는 `GL_TOKEN` |
| Bitbucket | `BITBUCKET_TOKEN` |

```bash
# 예: ~/.zshrc 또는 ~/.bashrc에 추가
export GITHUB_TOKEN="ghp_xxxxxxxxxxxx"
```

---

## 7. 플러그인 관리 CLI 명령어

### 7.1 플러그인 관리

```bash
# 설치
claude plugin install <plugin-name>@<marketplace> [--scope user|project|local]

# 제거
claude plugin uninstall <plugin-name>@<marketplace> [--scope user|project|local]

# 활성화/비활성화
claude plugin enable <plugin-name>@<marketplace>
claude plugin disable <plugin-name>@<marketplace>

# 업데이트
claude plugin update <plugin-name>@<marketplace>

# 유효성 검증
claude plugin validate .
```

### 7.2 마켓플레이스 관리

```bash
# 마켓플레이스 등록
claude plugin marketplace add <url-or-repo>

# 마켓플레이스 목록
claude plugin marketplace list

# 마켓플레이스 업데이트
claude plugin marketplace update <marketplace-name>

# 마켓플레이스 제거
claude plugin marketplace remove <marketplace-name>
```

### 7.3 대화형 UI

Claude Code 내에서 `/plugin` 명령으로 대화형 관리 UI에 접근할 수 있다.

| 탭 | 기능 |
|---|---|
| **Discover** | 등록된 마켓플레이스의 플러그인 탐색 |
| **Installed** | 설치된 플러그인 관리 |
| **Marketplaces** | 마켓플레이스 추가/제거/업데이트 |
| **Errors** | 플러그인 로딩 오류 확인 |

---

## 8. Standard Enforcer 배포 실행 계획

### 8.1 단계별 실행

#### 1단계: 플러그인 정비

```bash
# plugin.json의 author, homepage, repository 필드 업데이트
# README.md 작성 (사용법, 설치 방법, 기능 설명)
# LICENSE 파일 확인
# CHANGELOG.md 작성
# 유효성 검증
claude plugin validate .
```

#### 2단계: GitHub 저장소 생성

```bash
# 플러그인 저장소 (독립)
# https://github.com/your-org/standard-enforcer

# 마켓플레이스 저장소
# https://github.com/your-org/astra-claude-plugins
```

#### 3단계: 마켓플레이스 구성

```bash
# astra-claude-plugins 저장소에 marketplace.json 작성
# standard-enforcer를 등록
# git push
```

#### 4단계: 설치 테스트

```bash
# 다른 환경에서 테스트
/plugin marketplace add your-org/astra-claude-plugins
/plugin install standard-enforcer@astra-tools

# 기능 동작 확인
/standard-enforcer:lookup-term 고객명
/standard-enforcer:check-convention src/Main.java
```

#### 5단계: 팀 배포

```bash
# 프로젝트 .claude/settings.json에 마켓플레이스 등록
# 팀원에게 설치 안내
```

### 8.2 배포 체크리스트

- [ ] `plugin.json` 필드 완성 (name, version, description, author, license)
- [ ] `${CLAUDE_PLUGIN_ROOT}`를 사용한 경로 참조 확인
- [ ] README.md 작성
- [ ] LICENSE 파일 포함
- [ ] CHANGELOG.md 작성
- [ ] `claude plugin validate .` 통과
- [ ] 로컬 테스트 (`--plugin-dir`) 완료
- [ ] 마켓플레이스 저장소 생성
- [ ] `marketplace.json` 작성
- [ ] 설치 테스트 (`/plugin install`) 완료
- [ ] 스킬 자동 활성화 동작 확인
- [ ] 커맨드 실행 확인 (4개)
- [ ] 훅 동작 확인 (금칙어/네이밍)
- [ ] 팀 배포 (settings.json) 설정

---

## 9. 버전 관리 및 업데이트

### 9.1 시맨틱 버저닝

`MAJOR.MINOR.PATCH` 형식을 따른다.

| 변경 유형 | 버전 증가 | 예시 |
|---|---|---|
| 하위 호환 불가 변경 | MAJOR | 스킬 인터페이스 변경, 데이터 스키마 변경 |
| 기능 추가 | MINOR | 새 커맨드 추가, 새 언어 지원 |
| 버그 수정 | PATCH | 용어 사전 오류 수정, 스크립트 수정 |

### 9.2 업데이트 배포

```bash
# 1. plugin.json 버전 업데이트
# 2. CHANGELOG.md 작성
# 3. 커밋 및 태그
git commit -m "Release v1.1.0: Python 컨벤션 강화"
git tag v1.1.0
git push && git push --tags

# 4. marketplace.json의 version, ref, sha 업데이트
# 5. 마켓플레이스 저장소 푸시
```

### 9.3 사용자 업데이트

```bash
# 마켓플레이스 목록 갱신
claude plugin marketplace update astra-tools

# 플러그인 업데이트
claude plugin update standard-enforcer@astra-tools
```

---

## 10. 참고 자료

| 항목 | URL |
|---|---|
| 플러그인 만들기 가이드 | https://code.claude.com/docs/en/plugins |
| 마켓플레이스 생성/배포 가이드 | https://code.claude.com/docs/en/plugin-marketplaces |
| 플러그인 설치 가이드 | https://code.claude.com/docs/en/discover-plugins |
| 플러그인 기술 레퍼런스 | https://code.claude.com/docs/en/plugins-reference |
| 공식 플러그인 디렉토리 (GitHub) | https://github.com/anthropics/claude-plugins-official |
| 플러그인 제출 양식 | https://clau.de/plugin-directory-submission |
| 데모 플러그인 예시 | https://github.com/anthropics/claude-code/tree/main/plugins |
