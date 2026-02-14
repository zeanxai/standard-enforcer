# Standard Enforcer

A Claude Code plugin that automatically enforces coding conventions (Java, TypeScript, Python) and Korean public data standard terminology ([행정안전부 공공데이터 표준](https://www.data.go.kr)) when writing code.

Designed for teams that must comply with Korean government data standards and maintain consistent coding styles across projects.

## Features

### Auto-applied Skills (Zero Configuration)

Skills activate automatically based on the file you're editing — no manual invocation needed.

- **Coding Convention** — Enforces language-specific style guides when editing `.java`, `.ts`, `.tsx`, or `.py` files
  - Java: Google Java Style Guide (2-space indent, 100-char lines, K&R braces, no wildcard imports)
  - TypeScript: Google TS Style Guide (no `export default`, no `any`, no `var`, `===` required)
  - Python: PEP 8 (4-space indent, 79-char lines, `snake_case`, `is None`)

- **Data Standard Terminology** — Applies standard Korean public data naming when writing DB-related code (entities, SQL, DTOs, migrations, schemas)
  - Column names use government-standard English abbreviations (e.g., `CSTMR_NM` for 고객명)
  - Suffix patterns: `_YMD` (date), `_DT` (datetime), `_AMT` (amount), `_NM` (name), `_CD` (code), `_YN` (boolean)
  - Table prefixes: `TB_` (general), `TC_` (code), `TH_` (history), `TL_` (log), `TR_` (relation)

### Slash Commands

| Command | Description |
|---|---|
| `/standard-enforcer:lookup-term <term>` | Look up a Korean term's standard English abbreviation, domain, and data type |
| `/standard-enforcer:check-convention <file>` | Scan a file for coding convention violations and report issues by severity |
| `/standard-enforcer:check-naming <file or dir>` | Validate DB naming in entities, SQL, and DTOs against the standard dictionaries |
| `/standard-enforcer:generate-entity <definition>` | Generate standards-compliant entity code from Korean table/column definitions |

### PostToolUse Hooks (Automatic)

After every `Write` or `Edit` on DB-related files, two validation scripts run automatically in the background:

- **Forbidden Word Detection** — Flags banned words (금칙어) and suggests standard replacements
- **Naming Pattern Validation** — Checks table name prefixes and column naming patterns

Both hooks are non-blocking (exit 0) and surface warnings directly in Claude's context.

### Subagents (Deep Analysis)

- **convention-checker** — Deep convention inspection with confidence-based filtering to surface only high-priority issues
- **naming-validator** — Comprehensive DB naming validation against all three standard dictionaries (terms, words, domains)

## Data Sources

The plugin includes three government-standard reference dictionaries from the Korean Ministry of the Interior and Safety (행정안전부):

| Dictionary | Records | Description |
|---|---|---|
| `standard_terms.json` | 13,176 | Standard terms with Korean names, English abbreviations, and domains |
| `standard_words.json` | 3,284 | Standard words with abbreviations, forbidden words, and synonyms |
| `standard_domains.json` | 123 | Domain definitions with type codes, lengths, and decimal places |

## Installation

### From a Marketplace

```bash
# 1. Add the marketplace
/plugin marketplace add <marketplace-url>

# 2. Install the plugin
/plugin install standard-enforcer@<marketplace-name>
```

### Local Development

```bash
# Load from a local directory
claude --plugin-dir ./standard-enforcer

# Validate plugin structure
claude plugin validate .
```

### Project-wide Setup

Add the marketplace and plugin to your project's `.claude/settings.json` so that team members are prompted to install automatically:

```json
{
  "extraKnownMarketplaces": {
    "your-marketplace": {
      "source": {
        "source": "github",
        "repo": "your-org/your-marketplace"
      }
    }
  },
  "enabledPlugins": {
    "standard-enforcer@your-marketplace": true
  }
}
```

## Usage Examples

### Look Up a Standard Term

```
/standard-enforcer:lookup-term 가입일자
```

Returns the English abbreviation (`JOIN_YMD`), domain (`연월일C8`), data type, and language-specific type mappings.

### Check Coding Conventions

```
/standard-enforcer:check-convention src/main/java/Customer.java
```

Reports violations by severity (Error / Warning / Info) with line numbers and suggested fixes.

### Validate DB Naming

```
/standard-enforcer:check-naming src/main/java/entity/
```

Checks column names, table prefixes, suffix patterns, domain rules, and forbidden words against the standard dictionaries.

### Generate a Standards-compliant Entity

```
/standard-enforcer:generate-entity "고객: 고객명, 고객번호, 생년월일, 이메일주소, 가입일자, 사용여부"
```

Produces a mapping table, JPA entity class, TypeORM entity, SQLAlchemy model, and DDL — all using standard terminology.

## Plugin Structure

```
standard-enforcer/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── skills/
│   ├── coding-convention/    # Auto-applied coding convention skill
│   └── data-standard/        # Auto-applied data standard skill
├── commands/
│   ├── lookup-term.md        # /standard-enforcer:lookup-term
│   ├── check-convention.md   # /standard-enforcer:check-convention
│   ├── check-naming.md       # /standard-enforcer:check-naming
│   └── generate-entity.md    # /standard-enforcer:generate-entity
├── agents/
│   ├── convention-checker.md # Deep convention analysis subagent
│   └── naming-validator.md   # Deep naming validation subagent
├── hooks/
│   └── hooks.json            # PostToolUse hook configuration
├── scripts/
│   ├── check-forbidden-words.sh  # Forbidden word detection
│   └── validate-naming.sh        # Naming pattern validation
└── data/
    ├── standard_terms.json   # 13,176 standard terms
    ├── standard_words.json   # 3,284 standard words
    └── standard_domains.json # 123 domain definitions
```

## How It Works

```
Code write/edit request
  → Coding Convention skill (auto, for .java/.ts/.py files)
  → Data Standard skill (auto, for DB-related files)
  → Write/Edit tool execution
  → PostToolUse hooks (auto)
      → check-forbidden-words.sh (forbidden word scan)
      → validate-naming.sh (prefix & pattern check)
```

All enforcement happens transparently — no manual steps required. Simply write code and the plugin ensures compliance with both coding style and data naming standards.

## Supported Languages

| Language | Convention Base | Entity/ORM Support |
|---|---|---|
| Java | Google Java Style Guide | JPA / Hibernate |
| TypeScript | Google TypeScript Style Guide | TypeORM / Prisma |
| Python | PEP 8 | SQLAlchemy / Django |

## Author

**Zeans** — zeans@rudasoft.com

## License

MIT
