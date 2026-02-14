# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**standard-enforcer** is a Claude Code plugin that automatically enforces coding conventions (Java/TypeScript/Python) and Korean public data standard terminology when writing code. It targets teams that must comply with government data standards (공공 데이터 표준) and maintain consistent coding styles.

This is NOT a traditional application — it is a **Claude Code plugin** composed entirely of markdown prompts, shell scripts, and JSON data files. There is no build system, package manager, or compiled code.

## Architecture

The plugin follows the Claude Code plugin structure (`.claude-plugin/plugin.json` manifest) with five component types:

### Skills (Auto-invoked by Claude)
- **coding-convention** (`skills/coding-convention/SKILL.md`): Automatically applies language-specific conventions when writing/editing `.java`, `.ts`, `.tsx`, `.py` files. References detailed convention docs in the same directory.
- **data-standard** (`skills/data-standard/SKILL.md`): Automatically applies standard terminology when writing DB-related code (entities, SQL, DTOs, migrations, schemas).

### Commands (User-invoked via `/standard-enforcer:<command>`)
- `check-convention` — Scan files for coding convention violations
- `check-naming` — Validate DB naming against standard terminology
- `lookup-term` — Look up a Korean term's standard English abbreviation, domain, and data type
- `generate-entity` — Generate standards-compliant entity code from Korean table/column definitions

### Agents (Subagents for deep analysis)
- **convention-checker** (`agents/convention-checker.md`): Deep convention inspection with confidence-based filtering
- **naming-validator** (`agents/naming-validator.md`): Deep DB naming validation against all three standard dictionaries

### Hooks (PostToolUse, non-blocking)
After every `Write` or `Edit` on DB-related files, two shell scripts run automatically:
- `scripts/check-forbidden-words.sh` — Detects banned words (금칙어) and suggests standard replacements
- `scripts/validate-naming.sh` — Checks table name prefixes (TB_/TC_/TH_/TL_/TR_) and naming patterns

Both scripts exit 0 (non-blocking) and output warnings to Claude's context.

### Data (`data/`)
- `standard_terms.json` (13,176 records) — Standard terms with Korean names, English abbreviations, domains
- `standard_words.json` (3,284 records) — Standard words with abbreviations, forbidden words, synonyms
- `standard_domains.json` (123 records) — Domain definitions with type codes, lengths, decimal places

## Key Conventions

### DB Naming Standards
- Column names use standard abbreviations: 고객명 → `CSTMR_NM`, 가입일자 → `JOIN_YMD`
- Suffix patterns: `_YMD` (date), `_DT` (datetime), `_AMT` (amount), `_NM` (name), `_CD` (code), `_NO` (number), `_YN` (boolean Y/N), `_CN` (content)
- Table prefixes: `TB_` (general), `TC_` (code), `TH_` (history), `TL_` (log), `TR_` (relation)

### Coding Conventions by Language
- **Java**: Google Java Style (2-space indent, 100-char line, K&R braces, no wildcard imports)
- **TypeScript**: Google TS Style (no `export default`, no `any`, no `var`, no `.forEach()`, `===` required)
- **Python**: PEP 8 (4-space indent, 79-char line, `snake_case` functions, `is None` not `== None`)

## Data Flow

```
Code write/edit request
  → coding-convention skill (auto, for any .java/.ts/.py)
  → data-standard skill (auto, for DB-related files)
  → Write/Edit tool execution
  → PostToolUse hooks (auto)
      → check-forbidden-words.sh (금칙어 scan)
      → validate-naming.sh (prefix/pattern check)
```

## Testing

```bash
# Load plugin locally
claude --plugin-dir ./standard-enforcer

# Test commands
/standard-enforcer:lookup-term 가입일자
/standard-enforcer:check-convention src/main/java/Customer.java
/standard-enforcer:check-naming src/main/java/entity/
/standard-enforcer:generate-entity "고객: 고객명, 가입일자, 이메일주소"

# Debug mode
claude --debug
```

## File Modification Guidelines

- **Markdown files** (`commands/`, `skills/`, `agents/`): These are prompt definitions. Changes affect Claude's behavior directly.
- **Shell scripts** (`scripts/`): Must always `exit 0` (non-blocking). They receive PostToolUse event JSON via stdin and extract `tool_input.file_path`.
- **Data files** (`data/`): JSON dictionaries from the Korean government (행정안전부). Updated 1-2 times per year. Replace files to update — do not manually edit.
- **`hooks/hooks.json`**: Hook configuration referencing scripts via `${CLAUDE_PLUGIN_ROOT}` variable.
