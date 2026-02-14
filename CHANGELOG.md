# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-02-14

### Added

- **Skills**: Auto-applied coding convention skill for Java, TypeScript, and Python
- **Skills**: Auto-applied data standard terminology skill for DB-related code
- **Commands**: `/lookup-term` — Look up standard term abbreviations, domains, and data types
- **Commands**: `/check-convention` — Scan files for coding convention violations
- **Commands**: `/check-naming` — Validate DB naming against standard dictionaries
- **Commands**: `/generate-entity` — Generate standards-compliant entity code from Korean definitions
- **Agents**: `convention-checker` subagent for deep convention analysis
- **Agents**: `naming-validator` subagent for comprehensive naming validation
- **Hooks**: PostToolUse forbidden word detection (`check-forbidden-words.sh`)
- **Hooks**: PostToolUse naming pattern validation (`validate-naming.sh`)
- **Data**: 13,176 standard terms (`standard_terms.json`)
- **Data**: 3,284 standard words (`standard_words.json`)
- **Data**: 123 standard domains (`standard_domains.json`)
