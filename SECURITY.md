# Security Policy

## Supported builds

| Build | Supported |
|---|---|
| Latest release (`8.1.34-pl1-cve2026`) | Yes |
| Official PHP 8.1.34 and older 8.1 builds | No: vulnerable to CVE-2026-109618 and CVE-2026-109619 |

## Reporting a vulnerability

- **In these builds or this repository** (packaging, the build workflow, a missing or broken patch): report it privately through [GitHub private vulnerability reporting](https://github.com/ruban-s/php-8.1-windows-security/security/advisories/new). Do not open a public issue.
- **In PHP itself**: report it to the PHP project through [php-src security advisories](https://github.com/php/php-src/security). This repository does not develop PHP fixes. It only compiles fixes the PHP security team publishes.

## Scope

These builds contain PHP 8.1.34 plus the official backport for CVE-2026-109618 and CVE-2026-109619, and nothing else. PHP 8.1 is end of life, so other vulnerabilities fixed only in supported versions are not covered. [Upgrade to a supported version](https://www.php.net/supported-versions.php) when you can.
