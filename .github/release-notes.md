Unofficial PHP 8.1.34 Windows builds with the official PHP security team backport for two critical remote code execution vulnerabilities:

- **CVE-2026-109618** ([GHSA-w8hw-w9fx-j79w](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w)): RCE via OOM corruption
- **CVE-2026-109619** ([GHSA-6wmf-4rv5-p3rp](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp)): RCE via stale global state

Built from php-src [`1d7dec1`](https://github.com/php/php-src/commit/1d7dec178a094e6e75b229ce22754045f8c6070a) (branch `PHP-8.1-CVE-2026-109618-109619`) with the official php-windows-builder: Visual Studio 2019 (vs16), PGO. `php -v` reports `8.1.34-pl1-cve2026`.

| File | Use |
|---|---|
| `php-8.1.34-pl1-cve2026-nts-Win32-vs16-x64.zip` | IIS, FastCGI, CLI (64-bit) |
| `php-8.1.34-pl1-cve2026-Win32-vs16-x64.zip` | Apache mod_php (64-bit) |
| `php-8.1.34-pl1-cve2026-nts-Win32-vs16-x86.zip` | IIS, FastCGI, CLI (32-bit) |
| `php-8.1.34-pl1-cve2026-Win32-vs16-x86.zip` | Apache mod_php (32-bit) |
| `php-debug-pack-*` / `php-devel-pack-*` | PDB symbols / SDK for building extensions |
| `SHA256SUMS` | Checksums for every file |

Verify a download:

```powershell
gh attestation verify .\php-8.1.34-pl1-cve2026-nts-Win32-vs16-x64.zip --repo ruban-s/php-8.1-windows-security
```

Requires the Visual C++ 2015-2022 Redistributable. Upgrade steps, verification and FAQ: see the [README](https://github.com/ruban-s/php-8.1-windows-security#readme). PHP 8.1 is end of life, so plan an upgrade to a [supported version](https://www.php.net/supported-versions.php).
